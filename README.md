# RD80 Scripts

## Descrição

Este repositório contém um conjunto de ferramentas desenvolvidas para o processamento de dados de disdrômetros RD80 coletados em diferentes projetos de campo e campanhas observacionais. O código fornece rotinas para organização, pré-processamento e geração de saídas padronizadas, apoiando estudos de microfísica da precipitação e atividades de validação de estimativas de chuva por satélite.

Em particular, o repositório inclui scripts para gerar arquivos **netCDF** a partir de dados no formato `.trf`, bem como scripts para geração de **figuras e produtos gráficos** a partir dos arquivos netCDF processados.

Abaixo estão descritos os comandos disponíveis para execução dos scripts.

## Autores

Os principais autores e contribuidores deste software são:

1. **Alan James Peixoto Calheiros** – Concepção do projeto, supervisão científica e coordenação geral do desenvolvimento do software.  
2. **Thomaz A. Pougy** – Principal desenvolvedor do software, responsável por grande parte da implementação do código.  
3. **Helvécio Bezerra Leal Neto** – Refatoração do código, desenvolvimento de novas funcionalidades e elaboração da documentação.  
4. **Aline Andrade do Nascimento** – Apoio na implementação e implantação do software em diferentes ambientes de processamento.

Contribuições adicionais podem aparecer automaticamente na lista de colaboradores do GitHub.

## Instalação
### Requisitos
- Python 3.11 ou superior
- git
- pip
ou
- Anaconda

### Instalação
Via pip:
```bash
git clone https://github.com/SInApSE-INPE/datamap-curadoria.git
cd datamap-curadoria
pip install -r requirements.txt
```

Via conda:
```bash
git clone https://github.com/SInApSE-INPE/datamap-curadoria.git
cd datamap-curadoria
conda env create -f environment.yml
```


## Gen netCDF

### Executar para todos os arquivos `.trf` no diretório `JOSS/input/data`
Gera um arquivo netCDF para cada dia que possuir dados.

```bash
cd JOSS
```

```bash
python JOSS_gen_netCDF.py -s
```

### Executar para todos os arquivos em um diretório específico

```bash
python JOSS_gen_netCDF.py -s -i /path/to/directory
```

### Executar para uma data específica
Gera um arquivo netCDF apenas com os dados da data especificada no comando.

```bash
python JOSS_gen_netCDF.py -s -d 31/01/2000
```

### Executar para arquivos listados em `files.txt`
Gera um arquivo netCDF para cada dia que possuir dados dos arquivos cujos nomes estão incluídos no arquivo `files.txt` no diretório `input/JOSS`.

```bash
python JOSS_gen_netCDF.py -l
```

### Executar para uma data específica de arquivos listados em `files.txt`
Gera um arquivo netCDF apenas com os dados da data especificada no comando para os arquivos listados em `files.txt`.

```bash
python JOSS_gen_netCDF.py -l -d 31/01/2000
```

## Gen Figures

### Executar para todos os arquivos netCDF no diretório `input/JOSS/data_figures`
Gera todas as figuras nos formatos PNG e HTML.

```bash
python JOSS_gen_figures.py -s
```

### Executar para todos os arquivos netCDF no formato PNG
Gera apenas as figuras no formato PNG.

```bash
python JOSS_gen_figures.py -s -p
```

### Executar para arquivos listados em `files_figures.txt`
Gera todas as figuras para os arquivos cujos nomes estão incluídos no arquivo `files_figures.txt` no diretório `input/JOSS`.

```bash
python JOSS_gen_figures.py -l
```

### Executar para arquivos listados em `files_figures.txt` no formato PNG
Gera apenas as figuras no formato PNG.

```bash
python JOSS_gen_figures.py -l -p
```

## Estrutura de Diretórios

- `input/JOSS/data/`: Contém os arquivos `.trf` para gerar netCDF.
- `input/JOSS/data_figures/`: Contém os arquivos `.nc` para gerar figuras.
- `input/JOSS/files.txt`: Lista de arquivos `.trf` a serem processados.
- `input/JOSS/files_figures.txt`: Lista de arquivos `.nc` a serem processados.



### EXPLICACAO PATTERN MODE

#### exemplo de comando
```bash
python JOSS_gen_netCDF.py -p "%Y%m%d" -d "01/08/2022"
```

#### O que faz:

    Recebe o parâmetro -d e trata ele como uma data. Temos então datetime(year=2022,month=8,day=1).

    Gera 3 variáveis temporárias
        d0 = datetime(year=2022,month=8,day=1)
        d1 = datetime(year=2022,month=8,day=1) + day
        d2 = datetime(year=2022,month=8,day=1) - day

    Usa o valor recebido pelo parâmetro -p como um formatador de datetime. Assim formata d0, d1 e d2 como strings auxiliares. Veja:
        d0 = "20220801"
        d1 = "20220802"
        d2 = "20220731"

    Busca todos os arquivos no diretório de input, mas retorna na lista files, apenas aqueles arquivos cujo nome atenda aos critérios:
        1. nome de arquivo termina com a extensão especificada no veriables_info.json
        2. nome de arquivo contém alguma das string d0, d1 ou d2.

    A lista files é utilizada para ler os arquivos. Assim só lês os arquivos de interesse.

#### Importante notar então:

    A. Parâmetro -p
    
        Deve ser passado o formato que a data é registrada no nome dos arquivos que estão colocados no diretório input.
        Por exemplo, para os arquivos:
            RD-20220731-215400.txt
            RD-20220801-215400.txt
            RD-20220802-215400.txt
        
        Veja que em RD-20220801-215400.txt
                       ^^^^^^^^
                    Data está aqui

        O formato da data acima é YYYYMMDD
        Porém é necessário passar esse formato segundo as convenções do python strftime. Ver detalhes no link: https://strftime.org/
        Assim deveríamos passar como valor para o parâmetro -p -> %Y%m%d

    B. Parâmetro -d

        Determina para qual dia vamos ler um dia antes e depois.
        É necessário passar um parâmetro -d sempre que usar o parâmetro - p
