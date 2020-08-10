# Curso de introdução ao Linux para bioinformática clínica - Prática guiada DIA 3

Este curso tem como objetivos abordar de forma teórico-prática os **conceitos e comandos básicos na utilização de um sistema Linux** visando capacitar os alunos a aplicar e analisar dados na prática da bioinformática clínica. Ao final do curso, o aluno deverá ser capaz de compreender a estrutura de um sistema Linux, acesso remoto à servidores, comandos básicos de manipulação de arquivos e pastas, execução de comandos, edição de arquivos e pipeline de análises. Curso desenvolvido pela equipe de bioinformática do Hospital Israelita Albert Einstein.

***


* O comando **'sed'** ou Stream EDitor é usado para substituir texto de acordo com uma sequência de caracteres de interesse.  
Segue a lógica: sed 'OPERATION/REGEXP/REPLACEMENT/FLAGS' FILENAME  
```
sed 's/AAA/BBB/g' file_input.txt > file_output.txt
```
O argumento -i, permite modificar o próprio arquivo sem precisar gerar outro, mas cuidado se não tiver cópia.  
A letra 'g' no final significa procurar em toda a linha, não somente a primeira ocorrência.
```
sed -i '' 's/AAA/BBB/g' file_input.txt
```
Pode ser usado também para editar uma linhas específica, como somente a linha 3 no exemplo abaixo.
```
sed '3 s/AAA/BBB/' file_input.txt
```

## Contando elementos
* O comando **'wc'** ou word count conta o número de linhas, caracteres e palavras de um arquivo.
```
less sample_1.fastq
wc sample_1.fastq
wc -l sample_1.fastq
wc -c sample_1.fastq
wc -w sample_1.fastq
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

# Scripts 

Scripts, ou pequenos programas personalizados, servem para automatizar grandes tarefas e podem ser  
escritos em diversas linguagens de programação (como Python, PERL, bash, JAVA, R, C++, etc, etc, etc).  
Conhecer uma ou mais linguagens é essencial para análises de dados em Bioinformática, porém, lógica de  
programação e exemplos mais elaborados estão além do escopo desse curso e serão abordados em cursos futuros.  
Por hora, vamos exemplificar uma simples automatização de comando que pode ser feita diretamente na linha de comando,  
sem a necessidade de criar executáveis específicos. No exemplo abaixo, iremos usar um 'for loop' para executar  
um mesmo comando em sequência para diversos arquivos.

```
for i in $(ls *1.fastq); do sed -i '.ori' 's/\/1$//g' $i ; done
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

* O comando **'export'** pode ser usado para criar variáveis temporárias (nesse terminal) globais (acessíveis de qualquer diretório).  
O comando 'env' imprime as variáveis de ambiente existentes (não funciona no Windows)
```
export CURSO="curso_bioinfo"
echo $CURSO
echo $PWD
env
```

* O comando **'ln'** cria um link simbólico, uma cópia sem duplicar os dados salvando espaço em disco.
```
cd
cd dir1
ln -s list_dir.txt dir2/link1
ln -s $PWD/list_dir.txt dir2/link2
ls -l dir2/
less dir2/link1
less dir2/link2
```

* O comando **'rsync'** também pode ser utilizado para fazer cópias e transferir arquivos  
entre sistemas de forma segura. Ele já faz o SSH se necessário.

Sintaxe: O primeiro arquivo (origem) será sincronizado para o segundo local especificado (destino) no servidor.  
rsync meu_arquivo.txt user@IP:/path/meu_arquivo.txt


* O comando **'find'** pode ser usado para localizar arquivos pelo nome (não conteúdo) nos diretórios,  
seguindo a lógica: find <path> -name <searchstring>
```
find dir2 -name "link*"
``` 
Para procurar pelo tamanho do arquivo, como aqueles acima de 5 MB no diretório atual:
```
find . -size +5000k
```

# Exercícios
- No seu home, crie um novo diretório, entre dentro dele, crie um arquivo .txt contendo  
5 palavras, uma em cada linha, salve o arquivo, conte quantas linhas apresentam a letra 'e'  
maiúscula ou minúscula, ordene o arquivo por ordem alfabética e salve com um novo nome,  
por fim, substitua no novo arquivo a segunda palavra por uma nova sem usar o 'vi' e compare-os.  
  
- Pense em uma sequência de comandos para manipular um arquivo que contenha ao menos 4 pipelines.  

- O pacote samtools é utilizado para manipular arquivos resultantes do mapeamento de sequências em genomas,  
nos formatos .sam e .bam. Acesse o help do samtools para criar um comando utilizando pipes que ira  
converter o seu arquivo .sam e um .bam ordenado. Dica: samtools view, samtoos sort.
