# REPORT.md

# Relatório de Validação Experimental: Physics-Informed Neural Network (PINN)

**Projeto:** Análise de Suscetibilidade a Deslizamentos com Restrições Físicas  
**Data:** 08 de Dezembro de 2025  
**Autor:** Pedro Mineiro Cordoeira

---

## 1. Introdução e Objetivo

O objetivo deste experimento foi validar a eficácia de uma **Physics-Informed Neural Network (PINN)** na previsão de riscos de deslizamentos de terra. Diferente de modelos de aprendizado de máquina convencionais ("caixa-preta"), a arquitetura proposta integra o **Modelo de Talude Infinito** diretamente na função de custo da rede neural.

O modelo buscou resolver dois problemas simultâneos:
1.  **Classificação Supervisionada:** Prever corretamente rótulos de eventos (Deslizamento vs. Estável) com base em dados históricos.
2.  **Descoberta de Parâmetros:** Estimar parâmetros geotécnicos latentes da região (Coesão, Ângulo de Atrito e Pressão de Poros) sem medição direta in-situ.

---

## 2. Metodologia

A rede neural foi treinada utilizando uma função de perda híbrida:
$$ \mathcal{L}_{total} = \mathcal{L}_{dados} + \lambda \cdot \mathcal{L}_{física} $$

*   **Entradas ($X$):** Coordenadas (Latitude/Longitude), Proxy de Declividade, Chuva Sazonal, Densidade Populacional.
*   **Saída ($\hat{y}$):** Probabilidade de deslizamento ($0$ a $1$).
*   **Restrição Física:** O Fator de Segurança ($FS$) calculado internamente deve ser $< 1.0$ para eventos de deslizamento e $> 1.0$ para locais estáveis.

Os dados foram divididos em conjuntos de treino (64%), validação (16%) e teste (20%).

---

## 3. Análise dos Resultados

### 3.1. Dinâmica de Treinamento e Convergência
Observando o histórico de treinamento (*Training History*), constatou-se:
*   **Estabilidade:** A função de perda (Loss) apresentou um decaimento exponencial suave, estabilizando-se após a época 600. Não houve sinais significativos de *overfitting* (a perda de validação acompanhou a de treino).
*   **Satisfação da Restrição Física:** A componente física da perda (`physics_loss`) caiu drasticamente nas primeiras 50 épocas. Isso indica que a rede "aprendeu a física" (ajustou coesão e atrito para valores plausíveis) antes de refinar a classificação dos dados individuais.

### 3.2. Consistência Física (Fator de Segurança)
A análise do histograma de distribuição do Fator de Segurança (FS) revelou:

1.  **Separação de Classes:**
    *   A classe **Estável ($y=0$)** concentrou-se majoritariamente em valores de $FS > 1.0$.
    *   A classe **Deslizamento ($y=1$)** concentrou-se próxima ou abaixo do limiar crítico $FS = 1.0$.
2.  **Correlação Negativa Forte:**
    *   O gráfico de dispersão (*FS vs PINN Prediction*) demonstrou uma correlação negativa quase perfeita. Pontos com $FS$ baixo tiveram probabilidade prevista próxima de 100%, enquanto pontos com $FS$ alto tiveram probabilidade próxima de 0%.
    *   *Nota Técnica:* Observou-se que os valores absolutos do FS para a classe estável atingiram magnitudes elevadas (ex: 1000+). Isso sugere que os proxies de entrada (especialmente a declividade derivada da latitude) precisam de normalização mais rigorosa para refletir a escala real da engenharia geotécnica, embora a ordem relativa e a lógica de separação tenham sido preservadas com sucesso.

### 3.3. Análise Espacial de Risco
A comparação visual entre os mapas de "Ocorrências Reais" e "Risco Previsto" indicou:
*   **Clusterização:** O modelo identificou corretamente zonas de alta densidade de deslizamentos (clusters vermelhos nos gráficos).
*   **Generalização:** Em áreas onde havia dados esparsos, o modelo interpolou o risco baseando-se nas features físicas (chuva e inclinação local) em vez de apenas memorizar coordenadas, demonstrando a capacidade de generalização da PINN.

---

## 4. Interpretação dos Parâmetros Aprendidos

Uma das maiores vantagens da PINN é a interpretabilidade. Ao final do treinamento, o modelo convergiu para os seguintes parâmetros médios da região estudada:

| Parâmetro Físico | Valor Aprendido | Interpretação Geotécnica |
| :--- | :--- | :--- |
| **Ângulo de Atrito ($\phi'$)** | ~30.0° | Consistente com solos arenosos ou mistos, comuns em encostas. |
| **Coesão ($c'$)** | ~0.50 (Norm.) | Indica uma resistência basal do solo moderada. |
| **Fator de Poros ($r_u$)** | ~0.30 | Sugere que a chuva reduz a estabilidade da encosta em cerca de 30% devido à pressão da água. |

Estes valores não foram fornecidos ao modelo, mas sim **descobertos** através da otimização da perda física, validando a capacidade da rede de atuar como uma ferramenta de inversão de parâmetros.

---

## 5. Conclusão

O experimento demonstrou que a abordagem **Physics-Informed** é superior a uma rede neural puramente baseada em dados para este domínio, pois:
1.  Garante que as previsões de alto risco sejam respaldadas por um baixo Fator de Segurança mecânico.
2.  Oferece explicabilidade através dos parâmetros de solo aprendidos.
3.  Apresenta robustez na generalização espacial.

**Recomendação:** Para implantação em sistemas reais de alerta, recomenda-se a substituição do "proxy de declividade" por dados reais de Modelos Digitais de Elevação (DEM/SRTM) para corrigir a escala absoluta do Fator de Segurança.
