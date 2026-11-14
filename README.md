 **Alunos:** 
 - Luis Navarro Erbetta 
 - Beatriz Pessoa de Souza
 - Raquel Bentes Lima


 **Instituição:** Ilum Escola de Ciência - CNPEM


 **Disciplina:** Equações Diferencias


 **Professor:** Vinícius Francisco Wasques - Possui graduação (2012) e mestrado (2015) em Matemática pela UNESP e doutorado em Matemática Aplicada pela UNICAMP (2019). Atua na área de Matemática Aplicada e Computacional, com ênfase em Sistemas Fuzzy e Biomatemática 


**Linguagem:** Python


## Resumo

A modelagem matemática é essencial para a biologia de sistemas, pois permite simular a dinâmica da sinalização celular. Este trabalho analisa o uso de EDOs no mapeamento de vias bioquímicas, tendo como base o artigo de revisão de Matyszczak (2026). Parte-se da **Lei da Ação das Massas** e da **cinética de Michaelis-Menten** para converter redes biológicas em sistemas determinísticos. Como os modelos reais são complexos demais para resolução manual, a solução é computacional.

Como estudo de caso, aborda-se o modelo de Leung e Rangamani (2023), composto por **55 EDOs e 163 parâmetros**, que descreve como a variação nos estímulos de glutamato influencia os níveis de ATP e a plasticidade sináptica.


## Conteúdo do trabalho

1. **Introdução** — modelagem matemática em biologia de sistemas e papel das EDOs.
2. **Fundamentação teórica biológica** — sinalização celular, relação com doenças, sinalização de cálcio e metabolismo energético na sinapse.
3. **Fundamentação teórica matemática** — Lei da Ação das Massas, cinética de Michaelis-Menten (incluindo a aproximação do estado quase estacionário) e cinética de Hill.
4. **Modelo de Leung & Rangamani** — dinâmica do Ca²⁺, metabolismo de nucleotídeos de adenina e cascata AMPK/mTOR.
5. **Método computacional e numérico** — rigidez (*stiffness*), integradores implícitos, condições iniciais, análise de sensibilidade global e bifurcação.
6. **Simulação em Python** — figura que acopla a dinâmica de cálcio ao custo metabólico a partir de código autoral.
7. **Análise de resultados e discussão biológica.**
8. **Considerações finais.**
9. **Contribuição de cada autor.**

## Resumo da fundamentação matemática

**Lei da Ação das Massas.** Para um sistema com $n$ espécies e $m$ reações, a variação da concentração de cada espécie é a soma das taxas de todas as reações:

$$\frac{dC_i}{dt} = \sum_{j=1}^{m} r_{i,j}, \qquad r_{i,j} = (p_{i,j} - s_{i,j})\, k_j \prod_{l=1}^{n} [X_l]^{s_{l,j}}$$

**Michaelis-Menten.** Para o esquema $S + E \rightleftharpoons C \rightarrow E + P$, com a aproximação do estado quase estacionário ($d[C]/dt \approx 0$):

$$\frac{d[P]}{dt} = \frac{v_{max}[S]}{K_m + [S]}, \qquad v_{max} = k_2 [E]_0, \qquad K_m = \frac{k_{-1}+k_2}{k_1}$$

**Hill (ultrasensibilidade cooperativa).**

$$v = \frac{v_{max}[S]^n}{K_m^n + [S]^n}$$

Para $n > 1$ a resposta é sigmoidal, funcionando como um "contador biológico" do tipo tudo ou nada.

## Estudo de caso: cálcio e AMPK/mTOR

O modelo de Leung & Rangamani acopla a dinâmica do cálcio sináptico ao metabolismo de nucleotídeos de adenina e à via AMPK/mTOR (55 EDOs, 60 espécies químicas, 163 parâmetros cinéticos).

- **Cálcio:** variação do Ca²⁺ no citosol e no retículo endoplasmático a partir dos fluxos RyR, IP3R, SERCA, PMCA, vazamento do retículo e tamponamento.
- **Energia:** balanço de ATP, ADP e AMP, com contribuição da glicólise, da fosforilação oxidativa (modulada por Ca²⁺ via função de Hill), da adenilato quinase e do consumo pelas bombas iônicas.
- **Sinalização:** o aumento da razão AMP/ATP ativa a AMPK, que influencia os complexos mTORC1 e mTORC2.

### Métodos numéricos

O sistema é **rígido** (escalas de milissegundos a horas), o que torna métodos explícitos como RK4 impraticáveis. Usam-se métodos implícitos baseados em BDF:

- Artigo original: `ode15s` (MATLAB)
- Implementação em Python: `scipy.integrate.solve_ivp` com método `BDF` ou `Radau`

Para explorar o espaço de 163 parâmetros, o artigo de referência utiliza amostragem por Hipercubo Latino (LHS, N = 10.000) e o Coeficiente de Correlação Parcial de Posto (PRCC), além de análise de bifurcação em $k_{hyd}$ e na atividade do receptor de insulina.

## Simulação em Python

O código gera uma figura com dois painéis temporais sincronizados:

- **Painel superior:** Ca²⁺ citosólico e reticular.
- **Painel inferior:** ATP, ADP e AMP citosólicos.

O estímulo glutamatérgico (via IP3) é aplicado entre 2 s e 7 s.

**Condições iniciais (estado basal):**

| Variável | Valor |
|---|---|
| [Ca²⁺] citosólico | 0,1 µM |
| [Ca²⁺] retículo | 12,0 µM |
| [ATP] | 2,5 mM |
| [ADP] | 0,35 mM |
| [AMP] | 0,05 mM |

### Como executar

> Ajuste o nome do script e as dependências conforme o conteúdo real do repositório.

```bash
# clonar o repositório
git clone https://github.com/<usuario>/<repositorio>.git
cd <Trabalho final de Equações Diferenciais>

# instalar dependências
pip install numpy scipy matplotlib

# executar a simulação
python simulacao.ipynb
```

## Principais resultados

- No estado basal, o sistema está em homeostase: cálcio citosólico muito baixo e retículo como reservatório de alta concentração.
- Durante o estímulo, o Ca²⁺ sai do retículo para o citosol e as bombas SERCA e PMCA aumentam a atividade, consumindo ATP.
- A queda de ATP e o aumento de ADP/AMP elevam a razão AMP/ATP, principal gatilho de ativação da AMPK.
- Após o fim do estímulo, a depuração do cálcio e a fosforilação oxidativa restabelecem as reservas de ATP e o sistema retorna ao equilíbrio inicial.
- Em frequências acima de 10 Hz, observa-se maior perturbação metabólica e hiperativação da AMPK.
- A análise de bifurcação indica regiões de multistabilidade, com dois estados estáveis coexistentes que funcionam como um interruptor molecular ligado à síntese proteica e à Potenciação de Longo Prazo (LTP).

## Estrutura do repositório


```
.
├── README.md
├── EDO_apresentacao.pdf     # relatório do trabalho
├── simulacao.ipynb             # código da simulação (Figura 1)
└── figuras/
    └── grafico_resposta_sinaptica.png
    └── capa_do_README.png
```

## Contribuição dos autores

Todos os autores participaram da concepção, da execução e da escrita deste trabalho. 

• Luis Navarro Erbetta: fundamentação teórica matemática, aplicação computacional, discussão dos resultados finais e criação do GitHub.
• Beatriz Pessoa de Souza: fundamentação teórica biológica e considerações finais.
• Raquel Bentes Lima: resumo, introdução e organização do documento.

## Referências

1. LEUNG, A.; RANGAMANI, P. Computational modeling of AMPK and mTOR crosstalk in glutamatergic synapse calcium signaling. *npj Systems Biology and Applications*, 2023.
2. MATYSZCZAK, G. Mathematical and Computational Models of Biochemical Reactions and Cell Signaling — From Ordinary Differential Equations to Machine Learning. *International Journal of Molecular Sciences*, 2026.
3. MEYER, W. J. *Concepts of Mathematical Modeling*. Mineola, NY: Dover Publications, 2004.

