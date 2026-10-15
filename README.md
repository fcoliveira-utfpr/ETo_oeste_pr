# ETo no oeste do Paraná com dados NASA/POWER

![Visitas](https://visitor-badge.laobi.icu/badge?page_id=fcoliveira-utfpr.ETo_oeste_pr&left_text=visitas)
[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/fcoliveira-utfpr/ETo_oeste_pr/blob/main/analise_RBIC.ipynb)
![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![agrometeorologiapy](https://img.shields.io/badge/agrometeorologiapy-0.3.0-green)

Dados, código e resultados do artigo **"Desempenho de métodos alternativos de estimativa da evapotranspiração de
referência com dados NASA/POWER no oeste do Paraná"**, de Alexandre Luis Alegretti e Fabrício Correia de Oliveira
(Universidade Tecnológica Federal do Paraná, Câmpus Santa Helena).

> Este repositório é parte do artigo submetido à **Revista Brasileira de Iniciação Científica (RBIC)**, do IFSP,
> em 2026. O manuscrito não está incluído aqui enquanto estiver em avaliação.

## Resumo do estudo

A evapotranspiração de referência (ETo) diária foi estimada pelo método padrão de Penman-Monteith FAO 56 (PM-FAO56) e
por seis métodos alternativos (Hargreaves-Samani, Hargreaves-Samani calibrado regionalmente – GHS, Camargo, Turc,
Priestley-Taylor e Makkink) em dez municípios de clima Cfa do oeste do Paraná, com séries diárias do NASA/POWER de
1995 a 2025. Os métodos foram comparados ao PM-FAO56 por RMSE, MAE, viés, NSE, r, d e pelo índice c de Camargo e
Sentelhas.

| Método | RMSE (mm d⁻¹) | Viés (mm d⁻¹) | NSE | c | Desempenho |
|---|---|---|---|---|---|
| Turc | 0,49 | 0,00 | 0,912 | 0,936 | Ótimo |
| Priestley-Taylor | 0,65 | 0,27 | 0,837 | 0,918 | Ótimo |
| Makkink | 0,82 | −0,62 | 0,747 | 0,881 | Ótimo |
| Hargreaves-Samani | 0,86 | 0,28 | 0,722 | 0,822 | Muito bom |
| GHS | 1,23 | 0,87 | 0,409 | 0,750 | Bom |
| Camargo | 1,19 | −0,54 | 0,477 | 0,629 | Mediano |

Médias das oito séries meteorológicas independentes (Foz do Iguaçu, Medianeira e São Miguel do Iguaçu ocupam a mesma
célula da grade do NASA/POWER). Valores completos em `resultados/tabelas/desempenho_regional.csv`.

## Como executar

**No Google Colab:** clique no selo *Abrir no Colab* acima e use *Ambiente de execução > Executar tudo*. O notebook
instala as dependências e baixa todos os dados (NASA/POWER, malhas do IBGE e Natural Earth).

**Localmente:**

```bash
git clone https://github.com/fcoliveira-utfpr/ETo_oeste_pr.git
cd ETo_oeste_pr
pip install -r requirements.txt
jupyter notebook analise_RBIC.ipynb
```

Os dados já baixados estão em `resultados/dados/`; para baixá-los novamente, defina `FORCAR_DOWNLOAD = True` na
primeira célula de código.

## Organização dos arquivos

```mermaid
graph LR
    NB["analise_RBIC.ipynb"]
    subgraph ENT["Entradas (resultados/dados)"]
        POW["power_SIGLA.csv<br/>séries diárias NASA/POWER (10)"]
        MUN["municipios.csv"]
        GEO["pr_municipios.geojson<br/>uf.geojson · ne_lakes.zip"]
        META["metadados_download.json"]
    end
    subgraph SAI["Saídas"]
        ETO["resultados/dados/<br/>eto_diaria_municipios.csv.gz"]
        TAB["resultados/tabelas/*.csv"]
        FIG["figuras/*.png"]
    end
    API(["API NASA/POWER<br/>IBGE · Natural Earth"]) -->|download| POW & GEO
    POW & MUN & GEO --> NB
    NB --> ETO & TAB & FIG
    PKG(["agrometeorologiapy 0.3.0"]) -.->|cálculo da ETo| NB
```

```
ETo_oeste_pr/
├── analise_RBIC.ipynb             notebook completo da análise
├── requirements.txt
├── resultados/
│   ├── dados/
│   │   ├── municipios.csv          municípios, código IBGE, coordenadas e altitude
│   │   ├── power_CAS.csv ... power_TOL.csv   séries diárias do NASA/POWER (1995-2025)
│   │   ├── metadados_download.json  versão da API, fontes e data de acesso
│   │   ├── eto_diaria_municipios.csv.gz      variáveis e ETo diária por método
│   │   └── pr_municipios.geojson, uf.geojson, ne_lakes.zip   bases do mapa
│   └── tabelas/
│       ├── desempenho_regional.csv, desempenho_regional_amplitude.csv
│       ├── desempenho_por_municipio.csv
│       ├── classes_indice_c.csv
│       ├── caracterizacao_meteorologica.csv, eto_media_mensal_regional.csv
│       ├── correlacao_pearson.csv, correlacao_pearson_anomalias.csv
│       ├── vento_desempenho_series_independentes.csv, vento_desempenho_testes.csv
│       ├── sensibilidade_vento_WS2M.csv
│       └── resumo_numeros.json
└── figuras/                        figuras do artigo (PNG, 300 dpi)
```

## Dicionário de dados

**`power_SIGLA.csv`** (NASA/POWER, comunidade Agroclimatologia, uma linha por dia)

| Coluna | Descrição | Unidade |
|---|---|---|
| `T2M_MAX`, `T2M_MIN` | temperaturas máxima e mínima do ar a 2 m | °C |
| `RH2M` | umidade relativa do ar a 2 m | % |
| `PRECTOTCORR` | precipitação corrigida | mm d⁻¹ |
| `ALLSKY_SFC_SW_DWN` | radiação solar global | MJ m⁻² d⁻¹ |
| `TOA_SW_DWN` | radiação no topo da atmosfera | MJ m⁻² d⁻¹ |
| `WS2M`, `WS10M`, `WS50M` | velocidade do vento nativa a 2, 10 e 50 m | m s⁻¹ |
| `WSC` | velocidade do vento corrigida para 10 m sobre grama (`airportgrass`) | m s⁻¹ |

**`eto_diaria_municipios.csv.gz`** (uma linha por município e dia)

| Coluna | Descrição |
|---|---|
| `Tmax`, `Tmin`, `Tmed`, `UR`, `P`, `Rs` | variáveis meteorológicas de entrada |
| `u2` | vento a 2 m obtido de `WSC` pela FAO-56 (Eq. 47) |
| `Ra`, `Ra_POWER` | radiação no topo da atmosfera astronômica (usada) e do NASA/POWER |
| `Rso`, `BOC`, `BOL`, `Rn` | radiação de céu claro, balanços de ondas curtas e longas e saldo de radiação |
| `es`, `ea`, `Delta`, `gamma` | pressões de vapor, declividade da curva de saturação e constante psicrométrica |
| `PM`, `PM_rad`, `PM_aero` | ETo pelo PM-FAO56 e suas parcelas radiativa e aerodinâmica (mm d⁻¹) |
| `HS`, `GHS`, `Cam`, `Tur`, `PT`, `Mak` | ETo pelos métodos alternativos (mm d⁻¹) |

> Observação: a coordenada de Foz do Iguaçu usada na análise (−25,4708; −54,4824) situa-se no município vizinho de
> Santa Terezinha de Itaipu, na mesma célula da grade do NASA/POWER (detalhes em `municipios.csv`).

## Procedimentos principais

- Vento a 2 m: velocidade corrigida pelo NASA/POWER para 10 m sobre grama, convertida pelo perfil logarítmico da FAO-56;
  o `WS2M` nativo resultou em valores implausíveis (0,09 m s⁻¹ em Cascavel) e foi avaliado apenas como sensibilidade.
- PM-FAO56 com albedo de 0,23, Δ calculado em Tmed, Rs/Rso ≤ 1 e Ra astronômica no ponto de cada município.
- Makkink com o termo −0,12 mm d⁻¹; Camargo com F = 0,01 (temperatura média anual ≤ 23,5 °C).
- Métricas em mm d⁻¹; desempenho regional pela média das séries meteorológicas independentes.

## Referências dos dados e do software

- NASA Langley Research Center. POWER Project – Daily API v2.10.0. https://power.larc.nasa.gov/
- Oliveira, F. C. agrometeorologiapy: fórmulas de agrometeorologia em Python, v. 0.3.0.
  https://pypi.org/project/agrometeorologiapy/
- IBGE. Malhas territoriais (API de malhas). https://servicodados.ibge.gov.br/
- Natural Earth. Lakes and reservoirs, 1:10m. https://www.naturalearthdata.com/

## Contato

Fabrício Correia de Oliveira – fcoliveira@utfpr.edu.br – UTFPR, Câmpus Santa Helena
