# 🏔️ Physics-Informed Neural Networks (PINNs) para Análise de Deslizamentos

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-Deep%20Learning-red)
![Status](https://img.shields.io/badge/Status-Completed-success)

Este projeto implementa uma **Rede Neural Informada pela Física (PINN)** para prever a suscetibilidade a deslizamentos de terra. Diferente de abordagens tradicionais de "caixa preta", este modelo incorpora leis da mecânica dos solos (Estabilidade de Taludes) diretamente na função de perda da rede neural, garantindo que as previsões sejam fisicamente consistentes.

## 📋 Sobre o Projeto

O objetivo é criar um modelo híbrido que aprende a partir de dados históricos de eventos geológicos enquanto respeita restrições físicas fundamentais. O modelo é capaz de:

1.  **Prever Risco:** Classificar áreas como seguras ou propensas a deslizamentos.
2.  **Aprender Física:** Estimar parâmetros latentes do solo (coesão, ângulo de atrito) durante o treinamento.
3.  **Regularização Física:** Penalizar previsões que violem o cálculo do Fator de Segurança (FS).

### Principais Funcionalidades
*   **Geração de Dados Sintéticos:** Capacidade de gerar um dataset fictício para testes imediatos sem necessidade de arquivos externos.
*   **Arquitetura Híbrida:** Combina camadas densas (MLP) com equações diferenciais/algébricas de estabilidade.
*   **Visualização Avançada:** Mapas de risco espacial, distribuições de Fator de Segurança e histórico de convergência física.

---

## 📐 Fundamentação Física

O modelo baseia-se no **Modelo de Talude Infinito**. A estabilidade é governada pelo Fator de Segurança ($FS$):

$$ FS = \frac{c' + (\gamma z \cos^2 \beta - u) \tan \phi'}{\gamma z \sin \beta \cos \beta} $$

Onde a rede aprende os parâmetros desconhecidos ($c', \phi'$) e utiliza *proxies* dos dados de entrada para geometria ($\beta$) e pressão de poros ($u$).

*   **FS < 1.0**: Instável (Deslizamento)
*   **FS > 1.0**: Estável

---

## 🛠️ Estrutura do Projeto


├── Analysis.ipynb    # Notebook principal com todo o código (ETL, Modelo, Treino)
├── catalog.csv             # (Opcional) Arquivo de dados reais. Se ausente, usa dados sintéticos.
├── infer1.ipynb        # Código de teste com alguns resultados
├── README.md               # Documentação do projeto
└── requirements.txt        # Lista de dependências

---

🚀 Instruções de Execução
1. Pré-requisitos
Certifique-se de ter o Python instalado. As bibliotecas necessárias são:
numpy
pandas
torch (PyTorch)
matplotlib
seaborn
scikit-learn

---

2. Instalação
Clone este repositório e instale as dependências:
code
Bash
git clone https://github.com/seu-usuario/landslide-pinn.git
cd landslide-pinn
pip install numpy pandas torch matplotlib seaborn scikit-learn

---

Execute todas as células (Run All).
📊 Resultados Esperados
Ao final da execução, o notebook gerará os seguintes gráficos e análises:
Training History: Gráficos mostrando a queda da Loss de dados e da Loss física ao longo das épocas.
Parâmetros Aprendidos: O modelo imprimirá os valores estimados para Coesão, Ângulo de Atrito e Fator de Pressão de Poros.
Distribuição de FS: Um histograma comparando o Fator de Segurança calculado para áreas estáveis vs. áreas de deslizamento.
Mapa de Risco: Um gráfico de dispersão espacial comparando ocorrências reais com a probabilidade prevista pela Rede Neural.


---

📄 Licença
Este projeto é distribuído sob a licença MIT. Sinta-se livre para usar e modificar para fins acadêmicos e comerciais.