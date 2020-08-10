# Curso de introdução ao Linux para bioinformática clínica - Prática guiada DIA 1

Este curso tem como objetivos abordar de forma teórico-prática os **conceitos e comandos básicos na utilização de um sistema Linux** visando capacitar os alunos a aplicar e analisar dados na prática da bioinformática clínica. Ao final do curso, o aluno deverá ser capaz de compreender a estrutura de um sistema Linux, acesso remoto à servidores, comandos básicos de manipulação de arquivos e pastas, execução de comandos, edição de arquivos e pipeline de análises. Curso desenvolvido pela equipe de bioinformática do Hospital Israelita Albert Einstein.

***

# Acessando o servidor para prática

## Configurações e acesso aos servidores na nuvem
Todo o curso será realizado em servidores Linux (ubuntu) na AWS. Estes servidores possuem todas as ferramentas utilizadas no curso já instaladas, assim como as configurações necessárias. É importante lembrar que na maioria dos casos do dia-a-dia de um bioinformata, estas configurações e instalações podem ser necessárias e de responsabilidade do usuário.  
Observe as recomendações de acordo com seu Sistema Operacional (Windows/Mac/Linux) para logar.

**Usuário e senha**  
Cada aluno receberá uma mensagem no email cadastrado durante a inscrição com os dados de usuário e senha para acesso aos servidores.
Fique atento para as seguintes informações:  
**User  
Password  
IP  
Port**  

**Procedimento de acesso (Windows)**
* Fazer download e instalar [putty.exe](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
* Especificar os dados de acordo com informações recebidas no email: Remote host (IP), username, port 
* Clique em OPEN para iniciar a sessão

![alt text](https://github.com/Varstation/curso-basico-linux/blob/master/img/putty_login.png "Putty")  
**Figura 1:** Tela de login do programa Putty para Windows.

**Procedimento de acesso (Mac/Linux)**
* Localize o aplicativo "Terminal" e execute o comando **'ssh'** de acordo com informações recebidas no email: Remote host (IP), username, port.
```
ssh -p port username@IP
``` 

# Familiarizando com o ambiente Linux

## Navegando entre arquivos e pastas
  
~  >> home  
.. >> acima  
.  >> atual  
\-  >> último  

* O comando **'ls'** serve para listar o conteúdo do diretório local...
```
ls
ls .
```
... de um diretório RELATIVO ao que você se encontra ...
```
ls ../
```
... ou um diretório qualquer no sistema, desde que você forneça seu 'path' completo, ou ABSOLUTO.
```
ls /home/yourusername
```

Os caracteres '../' são usados para se referir aos diretórios ACIMA e podem ser usados em sequência '../../../'  
Experimente utilizar a tecla TAB para completar os nomes dos diretórios.


* O comando **'man'** mostra o manual completo para o comando desejado. Use ESC para voltar ao seu prompt.
```
man ls
```

* O comando **'pwd'** ou Print Working Directory imprime na tela seu diretório local atual e é útil para se localizar.
```
pwd
```

* O comando **'mkdir'** ou make directory, cria pastas.
```
mkdir dir1
ls
ls dir1
mkdir dir1/dir2
mkdir dir3 dir4
ls
```

* O comando **'cd'** ou change directory é usado para navegar entre as pastas, seria como o duplo clique do mouse na interface gráfica do Windows.  
Somente 'cd' sem argumento, retorna para seu /home.  
cd com - retorna para o último diretório visitado.
```
cd dir1
ls
cd
cd -
```

## Manipulando arquivos e pastas
* O comando **'touch'** é usado para criar arquivos vazios com o nome fornecido.
```
touch file1.txt
ls
```

* O comando **'cp'** de copy é utilizado para copiar arquivos.
```
cp file1.txt file2.txt
ls
```

* O comando **'mv'** de move é utilizado para mover arquivos e pastas de um diretório para outro.  
Também pode ser usado para renomear arquivos
```
mv file1.txt dir2/
mv file2.txt file3.txt
mv file3.txt ../dir3
```

* O comando **'rm'** de remove e **'rmdir'** de remove directory são utilizados para remover, deletar arquivos e diretórios (vazios).  
As opções -rf significam recursivo e forçar. Devem ser usadas com MUITO cuidado, pois não tem volta. Tudo dentro do diretório será deletado.
```
rm dir2/file1.txt
rmdir ../dir4
rmdir ../dir3
rm -rf ../dir3
```
