# Curso de introdução ao Linux para bioinformática clínica - Prática guiada DIA 3

Este curso tem como objetivos abordar de forma teórico-prática os **conceitos e comandos básicos na utilização de um sistema Linux** visando capacitar os alunos a aplicar e analisar dados na prática da bioinformática clínica. Ao final do curso, o aluno deverá ser capaz de compreender a estrutura de um sistema Linux, acesso remoto à servidores, comandos básicos de manipulação de arquivos e pastas, execução de comandos, edição de arquivos e pipeline de análises. Curso desenvolvido pela equipe de bioinformática do Hospital Israelita Albert Einstein.

***


* O comando **'sed'** ou Stream EDitor também é usado para editar arquivos.  
As substituições seguem a mesma lógica: sed 'OPERATION/REGEXP/REPLACEMENT/FLAGS' FILENAME  
Ex: sed 's/AAA/BBB/g' file_input.txt > file_output.txt
```
sed 's/NC_045512.2_cds/NC_045512/g' sample.fasta > sample.edit.fasta
```
O argumento -i, permite modificar o próprio arquivo sem precisar gerar outro, mas cuidado se não tiver cópia.  
A letra 'g' no final significa procurar em toda a linha, não somente a primeira ocorrência.
```
sed -i 's/NC_045512.2/NC_045512/g' sample.fasta
```
Pode ser usado também para editar uma linha específica, como somente a linha 1 no exemplo abaixo.
```
sed -i '1 s/NC_045512/ID/' sample.fasta
```
Pode ser usado para remover linhas, como especificamente a segunda linha:
```
sed '2d' sample.fasta | less
```
...ou um intervalo específico:
```
sed '2,306d' sample.fasta | less
```
## Contando elementos
* O comando **'wc'** ou word count conta o número de linhas, caracteres e palavras de um arquivo.
```
less sample_1.fastq
wc sample_1.fastq
wc -l sample_1.fastq
wc -w sample_1.fastq
wc -c sample_1.fastq
```		

## Dividindo arquivos
* O comando **'split'** pode ser usado para dividir arquivos em partes.  
Suponha que desejamos dividir o arquivo em 4 de 100 linhas cada (25 sequências).
```
split -l 100 sample_1.fastq
```

## Ordenando, filtrando e selecionando colunas

* Os comandos **'sort'**, **'uniq'** e **'awk'** são extremamente úteis na manipulação e filtragem de arquivos.  
Vamos combiná-los em um pipeline junto com outros comandos para analisar um arquivo vcf.  
Fazendo passo a passo:
- Use 'zcat' para acessar o arquivo zipado sem precisar descompacta-lo:
```
zcat variants.vcf.gz | less
```
- Use 'grep -v' para desconsiderar as linhas do cabeçalho:
```
zcat variants.vcf.gz | grep -v "#" | less
```
- Use 'awk' para acessar a primeira colunas do arquivo:
```
zcat variants.vcf.gz | grep -v "#" | awk '{print$1}' | less
```
- Use 'awk' para filtrar somente linhas onde a coluna 7 (Filter) possui a palavra PASS:
```
zcat variants.vcf.gz | grep -v "#" | awk '$7=="PASS"{print$1}' | less
```
- O 'sort' pode ser usado para ordenar o resultado (no nosso exemplo, já estava antes):
```
zcat variants.vcf.gz | grep -v "#" | awk '$7=="PASS"{print$1}' | sort | less
```
- Use 'uniq' para obter somente resultado únicos (os dados precisam estar ordenados):
```
zcat variants.vcf.gz | grep -v "#" | awk '$7=="PASS"{print$1}' | sort | uniq | less
```
- Por fim, use 'uniq -c' para contar o número de variantes por cromossomo:
```
zcat variants.vcf.gz | grep -v "#" | awk '$7=="PASS"{print$1}' | sort | uniq -c | less
```
- Ordene por ordem númerica:
```
zcat variants.vcf.gz | grep -v "#" | awk '$7=="PASS"{print$1}' | sort -n | uniq -c | less
```

Observe a importância e a praticidade de utilizar '|', evitando o acúmulo de vários  
comandos individuais e arquivos intermediários.  

Vamos usar o awk para criar um arquivo .BED a partir do VCF.  
- Selecione as colunas do cromossomo e da coordenada:
```
zcat variants.vcf.gz | grep -v "#" | awk '$7=="PASS"{print$1,$2}' | less
```
- Ajuste as coordenadas de início e final (lembrando que .bed é *zero-based*):
```
zcat variants.vcf.gz | grep -v "#" | awk '$7=="PASS"{print$1,$2-1,$2}' | less
 ```
- Insira um caracter *tab* entre as colunas impressas:
```
zcat variants.vcf.gz | grep -v "#" | awk '$7=="PASS"{print$1"\t"$2-1"\t"$2}' > variants.bed
```

E se meu arquivo não esta separado por *tab* mas por vírgulas (csv)?:
```
zcat variants.vcf.gz | grep -v "#" | awk '$7=="PASS"{print$1","$2-1","$2}' > test.csv
cat test.csv | awk '{print$1}' | less
```

Basta informar para o 'awk' que o separador é a vírgula:
```
cat test.csv | awk -F "," '{print$1}' | less
cat test.csv | awk -F "," '{print$2}' | less
```

# Scripts 

Scripts, ou pequenos programas personalizados, servem para automatizar grandes tarefas e podem ser  
escritos em diversas linguagens de programação (como Python, PERL, JAVA, R, C++, etc, etc, etc).  
Conhecer uma ou mais linguagens é essencial para análises de dados em Bioinformática, porém, lógica de  
programação e exemplos mais elaborados estão além do escopo desse curso e serão abordados em cursos futuros.  
Por hora, vamos exemplificar uma simples automatização de comando que pode ser feita diretamente na linha de comando,  
ou com um pequeno script. Nos exemplos abaixo, iremos usar um 'for loop' para executar comandos em sequência.  

```
less sample_2.fastq
for var in $(ls *2.fastq); do sed -i 's/\/2$//g' $var ; done
```


# Comandos adicionais

* O comando **'scp'** é muito utilizado para transferir arquivos entre computadores.

Copiar um arquivo (file.txt) em outra maquina (IP) para o presente diretório local (.) usando a porta XXX.
```
scp -P XXX user@IP:/path/to/file/file.txt .
```
Copiar um arquivo do presente diretório local (file.txt) para outra maquina(IP) usando a porta XXX.
```
scp -P XXX file.txt user@IP:/path/to/directory
```


* O comando **'find'** pode ser usado para localizar arquivos pelo nome (não conteúdo) nos diretórios,  
seguindo a lógica: find <path> -name <searchstring>
```
find . -name "sample*"
``` 
Para procurar pelo tamanho do arquivo, como aqueles acima de 5 MB no diretório atual:
```
find . -size +50k
```

# Exercícios
- No seu home, crie um novo diretório, entre dentro dele, crie um arquivo.txt contendo  
5 palavras, uma em cada linha, salve o arquivo, conte quantas linhas apresentam a letra 'e'  
maiúscula ou minúscula, ordene o arquivo por ordem alfabética e salve com um novo nome,  
por fim, substitua no novo arquivo a segunda palavra por uma nova sem usar o 'vi' e compare-os.  
  
- Pense em uma sequência de comandos para manipular um arquivo que contenha ao menos 4 pipelines.  

- O pacote samtools é utilizado para manipular arquivos resultantes do mapeamento de sequências em genomas,  
nos formatos .sam e .bam. Acesse o help do samtools para criar um comando utilizando pipes que ira  
converter o seu arquivo .sam e um .bam ordenado. Dica: samtools view, samtoos sort.

  

# BONUS TRACK
Vamos criar um script bash, revisando alguns dos comandos?  
Na pasta contendo os arquivos .fastq, utilize o 'vi' para criar um arquivo chamado script.sh com o conteúdo abaixo:  
```
#!/usr/bin/bash
#Imprimir na tela
echo "Iniciando script..."

# Iniciar loop 1
for file in $(ls *1.fastq);
 do split -l 100 $file; 
 mkdir split_folder;

    i=1; #Iniciar contador
    #Iniciar loop 2
    for file_split in $(ls x*);
    do  mv $file_split split_folder/parte$i.fastq;
        wc -l split_folder/parte$i.fastq
       ((i++)); #Somar contador dentro do loop 2

    done; #Finalizar loop2
done; #Finalizar loop 1

#imprimor fim na tela
echo "FIM..."
```

Agora, execute seu script e observe o resultado:
```
bash script.sh
```
