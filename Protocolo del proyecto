# Protocolo del proyecto

## Descarga de archivos
Desde la ruta /export/space3/users/llozano/GENOMICA/DATA/ se obtuvieron los siguientes archivos:
  - ILLU_GRP4_1.fq
  - ILLU_GRP4_2.fq
  - PAC_GRP3.fastq
Los archivos se guardaron en PROYECTO_EQUIPO_4/DATA

## Desarrollo del proyecto

Creamos el directorio para guardar los resultados del FASTQC:
```
mkdir FASTQS
cd FASTQS
```

Luego se realizón un FASTQC con los siguientes comandos:
```
fastqc ../DATA/ILLU_GRP4_* 
fastqc ../DATA/PAC_GRP4.fastq 
```

Una vez obtenidos los resultados del FASTQC procedimos a visualizarlos, donde nos dimos cuenta que aproximadamente
a partir del 12 por lo que procedimos a correr el comando tomando en cuenta solo los valores delante del 12

Creamos el directorio para guardar los resultados de TRIMM (TRIMMOMATIC y TRIM_GALORE):
```
mkdir TRIMM
cd TRIMM
mkdir TRIMMOMATIC TRIM_GALORE
```

Primero creamos las lineas simbolicas a los archivos tanto del proyecto como del archivo de adaptadores que 
previamente se nos dió y luego corrimos ambos programas con los siguientes comandos:
```
cd TRIM_GALORE
ln -s ../../data/ILLU_GRP4_1.fq 
ln -s ../../data/ILLU_GRP4_2.fq
ln -s /export/storage/users/addielpr/GENOMICA/TRIMM/TRIMMOMATIC/TruSeq3-PE.fa
trim_galore --fastqc -j 4 --clip_R1 12 --clip_R2 12 --paired --retain_unpaired ../../data/ILLU_GRP4_1.fq ../../data/ILLU_GRP4_2.fq

cd ../TRIMMOMATIC
ln -s ../../data/ILLU_GRP4_1.fq 
ln -s ../../data/ILLU_GRP4_2.fq
ln -s /export/storage/users/addielpr/GENOMICA/TRIMM/TRIMMOMATIC/TruSeq3-PE.fa
trimmomatic PE ILLU_GRP4_1.fq ILLU_GRP4_2.fq ILLU_GRP4_1_pair.fq ILLU_GRP4_1_unpair.fq ILLU_GRP4_2_pair.fq ILLU_GRP4_2_unpair.fq
ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:20 MINLEN:25
```
