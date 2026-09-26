# 🛰️ Classificação de Uso do Solo com CNN

> Classificação automática de imagens de satélite utilizando Redes Neurais Convolucionais (CNN) e Deep Learning.

## 📌 Sobre o projeto

Este projeto foi desenvolvido para a disciplina **Deep Learning e Visão Computacional** da FIAP, com o objetivo de aplicar Redes Neurais Convolucionais na classificação automática de imagens de satélite.

O modelo utiliza o **UC Merced Land Use Dataset**, composto por **2.100 imagens distribuídas em 21 classes de uso do solo**, buscando aprender padrões visuais capazes de diferenciar diferentes tipos de ambientes e estruturas presentes nas imagens.

A proposta está relacionada ao contexto da **Nova Economia Espacial**, explorando como técnicas de Inteligência Artificial podem automatizar a análise de grandes volumes de imagens obtidas por satélites.

---

## 🎯 Objetivo

Desenvolver e avaliar uma **Rede Neural Convolucional (CNN)** capaz de classificar imagens de satélite em **21 categorias de uso do solo**, buscando boa capacidade de generalização para imagens não utilizadas durante o treinamento.

### Possíveis aplicações

* 🌳 Monitoramento ambiental
* 🏙️ Planejamento urbano
* 🌾 Agricultura de precisão
* 🛰️ Análise geoespacial
* 🏗️ Monitoramento de infraestrutura
* 🌎 Inteligência territorial

---

## 🗂️ Dataset

O projeto utiliza o **UC Merced Land Use Dataset**.

| Característica     | Informação |
| ------------------ | ---------- |
| Imagens            | 2.100      |
| Classes            | 21         |
| Imagens por classe | 100        |
| Resolução original | 256 × 256  |
| Canais             | RGB        |
| Formato            | `.tif`     |

O dataset é balanceado, contendo exatamente 100 imagens para cada uma das 21 classes.

### Classes

```text
agricultural
airplane
baseballdiamond
beach
buildings
chaparral
denseresidential
forest
freeway
golfcourse
harbor
intersection
mediumresidential
mobilehomepark
overpass
parkinglot
river
runway
sparseresidential
storagetanks
tenniscourt
```

---

## ⚙️ Pipeline

O projeto foi estruturado em diferentes etapas:

```text
Imagens .tif
     ↓
Conversão para RGB
     ↓
Resize 256×256 → 64×64
     ↓
Normalização [0, 1]
     ↓
Divisão Estratificada
     ↓
Data Augmentation
     ↓
CNN
     ↓
Treinamento
     ↓
Avaliação
     ↓
Matriz de Confusão + Métricas
```

### Pré-processamento

As imagens foram convertidas para RGB, redimensionadas para **64×64 pixels** e normalizadas para o intervalo `[0,1]`.

Os dados foram divididos de forma estratificada em:

* **70% — Treinamento**
* **15% — Validação**
* **15% — Teste**

O Data Augmentation foi aplicado somente ao conjunto de treinamento.

---

## 🧠 Arquitetura da CNN

A rede foi construída utilizando **3 blocos convolucionais progressivos**, aumentando a quantidade de filtros de `32 → 64 → 128`.

```text
Input (64×64×3)
        ↓
Conv2D(32)
BatchNormalization
Conv2D(32)
MaxPooling
Dropout(0.25)
        ↓
Conv2D(64)
BatchNormalization
Conv2D(64)
MaxPooling
Dropout(0.25)
        ↓
Conv2D(128)
BatchNormalization
Conv2D(128)
MaxPooling
Dropout(0.40)
        ↓
GlobalAveragePooling2D
        ↓
Dense(256)
BatchNormalization
Dropout(0.50)
        ↓
Dense(21, Softmax)
```

A progressão dos filtros busca permitir que a rede aprenda desde características mais simples, como bordas e texturas, até padrões e estruturas mais complexas presentes nas imagens.

### Principais componentes

**Batch Normalization**

Utilizado após as camadas convolucionais para estabilizar as ativações e auxiliar na convergência do treinamento.

**Global Average Pooling**

Utilizado no lugar de um `Flatten` tradicional, reduzindo a quantidade de parâmetros da parte totalmente conectada da rede.

**Dropout**

Aplicado progressivamente (`0.25 → 0.40 → 0.50`) como técnica de regularização para reduzir overfitting.

---

## 🔄 Data Augmentation

Para aumentar a diversidade das imagens de treinamento, foram utilizadas transformações como:

* Rotação de ±20°
* Horizontal Flip
* Vertical Flip
* Shift de 10%
* Zoom de 10%
* `fill_mode="reflect"`

As transformações foram aplicadas exclusivamente às imagens de treinamento para evitar vazamento de informação dos conjuntos de validação e teste.

---

## 🚀 Treinamento

O modelo foi configurado com:

| Configuração          | Valor                             |
| --------------------- | --------------------------------- |
| Otimizador            | Adam                              |
| Learning Rate inicial | `1e-3`                            |
| Loss                  | `sparse_categorical_crossentropy` |
| Épocas máximas        | 80                                |
| EarlyStopping         | `patience=15`                     |
| ReduceLROnPlateau     | `factor=0.5`                      |
| Classes               | 21                                |

O **EarlyStopping** monitora a acurácia de validação e restaura os melhores pesos encontrados durante o treinamento. Já o **ReduceLROnPlateau** reduz o learning rate quando a loss de validação deixa de melhorar.

---

## 📊 Avaliação

A avaliação foi realizada utilizando um conjunto de teste separado do treinamento e da validação.

Foram consideradas:

* **Accuracy**
* **Precision**
* **Recall**
* **F1-Score**
* **Matriz de Confusão**
* Curvas de **Loss**
* Curvas de **Accuracy**

A matriz de confusão permite analisar quais classes são mais facilmente confundidas pelo modelo.

### 🔎 Principais desafios

As maiores dificuldades estão concentradas em classes visualmente semelhantes, principalmente entre diferentes níveis de áreas residenciais:

```text
denseresidential
        ↕
mediumresidential
        ↕
sparseresidential
```

Também existe sobreposição visual entre classes como:

```text
agricultural ↔ forest
buildings ↔ denseresidential
```

Essas confusões estão relacionadas principalmente à similaridade dos padrões visuais presentes nas imagens.

---

## 🌎 Aplicação na Nova Economia Espacial

A classificação automática de imagens de satélite pode ser utilizada como parte de pipelines de análise geoespacial em larga escala.

Uma aplicação em produção poderia integrar modelos desse tipo a fontes de imagens como **Sentinel-2** ou **Planet**, permitindo a classificação automática de grandes volumes de tiles geoespaciais.

Exemplos de aplicações:

* Monitoramento ambiental
* Detecção de mudanças no uso do solo
* Planejamento urbano
* Monitoramento de infraestrutura
* Inteligência territorial
* Análise de áreas agrícolas

---

## ⚠️ Limitações

O projeto apresenta algumas limitações importantes:

### Dataset

Cada classe possui apenas 100 imagens, limitando a quantidade de dados disponível para treinamento.

### Resolução

O redimensionamento para `64×64` reduz o custo computacional, mas pode eliminar detalhes importantes para diferenciar classes visualmente semelhantes.

### Generalização geográfica

O dataset foi construído a partir de regiões dos Estados Unidos, podendo apresentar limitações de generalização para outras regiões, como o Brasil.

### Classificação estática

O modelo trabalha com imagens individuais e não considera mudanças temporais no uso do solo.

---

## 🛠️ Tecnologias

* Python
* TensorFlow / Keras
* NumPy
* Pandas
* Matplotlib
* Scikit-learn
* tifffile
* Deep Learning
* Computer Vision
* Convolutional Neural Networks (CNN)

---

## 📁 Estrutura do projeto

```text
.
├── data/
│   └── UC_Merced_LandUse/
│
├── notebooks/
│   └── classificacao_uso_solo.ipynb
│
├── models/
│   └── ...
│
├── results/
│   ├── confusion_matrix/
│   ├── training_curves/
│   └── metrics/
│
├── README.md
├── requirements.txt
└── .gitignore
```

> A estrutura acima pode ser adaptada à organização final do repositório.

---

## 👨‍💻 Autores

**João Victor Tozzatti Matiro**
FIAP — Tecnólogo em Inteligência Artificial

**Pedro Diagro Lopes**
FIAP — Tecnólogo em Inteligência Artificial

Projeto desenvolvido para a disciplina **Deep Learning e Visão Computacional — Global Solution 2026.1**.

---

## 📄 Licença

Este projeto foi desenvolvido para fins acadêmicos.
