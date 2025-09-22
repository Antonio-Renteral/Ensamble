# Protocolo del proyecto

## Obtención de archivos
Desde la ruta /export/space3/users/llozano/GENOMICA/DATA/ se obtuvieron los siguientes archivos:
  - ILLU_GRP4_1.fq
  - ILLU_GRP4_2.fq
  - PAC_GRP3.fastq

## Desarrollo del proyecto (comandos):

Los archivos se guardaron en PROYECTO_EQUIPO_4/DATA mediante lineas simbólicas:
```
mkdir PROYECTO_EQUIPO_4
cd PROYECTO_EQUIPO_4
mkdir DATA
cd DATA
ln -s ../../DATA/ILLU_GRP4_1.fq
ln -s ../../DATA/ILLU_GRP4_2.fq
ln -s export/storage/users/addielpr/GENOMICA/TRIM/TRIMMOMATIC/TruSeq3-PE.fa
```

Creamos el directorio para guardar los resultados del FASTQC:
```
mkdir FASTQS
cd FASTQS
```

Luego se realizó un FASTQC con los siguientes comandos:
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
ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:20 MINLEN:25
```

Visualizamos y comparamos los resultados obtenidos tanto por FASTQC como por TRIM_GALORE y TRIMMOMATIC. Dandonos cuenta
que los que mejor se veian eran los obtenidos mediante FASTQC por lo que los siguientes pasos se trabajaron con los resultados de los FASTQS

Creamos la carpeta ENSAMBLE y dentro de esta una llamada VELVET_OPTIMISER. Posteriormente corrimos el VelvetOptimiser con rangos diferentes
para ver cual era el mejor valor:
```
mdkir ENSAMBLE
cd ENSAMBLE
mkdir VELVET_OPTIMISER
cd VELVET_OPTIMISER
ln -s ../../DATA/ILLU_GRP4_1.fq
ln -s ../../DATA/ILLU_GRP4_2.fq
VelvetOptimiser.pl --s 21 --e 61 --x 2 --t 16 -f '-fastq -shortPaired -separate ILLU_GRP4_1.fq ILLU_GRP4_2.fq'
VelvetOptimiser.pl --s 31 --e 51 --x 2 --t 16 -f '-fastq -shortPaired -separate ILLU_GRP4_1.fq ILLU_GRP4_2.fq'
VelvetOptimiser.pl --s 35 --e 47 --x 2 --t 16 -f '-fastq -shortPaired -separate ILLU_GRP4_1.fq ILLU_GRP4_2.fq'
```

De acuerdo a los resultados, el mejor valor fue el 43

Proseguimos creando una carpeta para ejecutar velveth y otra para ejecutar velvetg. Mismas que están dentro de la carpeta ENSAMBLE. 

```
mkdir VELVETH
mkdir VELVETG   
```

Velveth y Velvetg se corrieron con los siguientes comandos:
```
cd VELVETH
ln -s ../../DATA/ILLU_GRP4_1.fq
ln -s ../../DATA/ILLU_GRP4_2.fq
velveth VELVET_43 43 -fastq  -shortPaired -separate ILLU_GRP4_1.fq ILLU_GRP4_2.fq
cd ../VELVETG
ln -s ../../DATA/ILLU_GRP4_1.fq
ln -s ../../DATA/ILLU_GRP4_2.fq
ln -s ../VELVETH/VELVET_43/ 
velvetg VELVET_43 -cov_cutoff auto -exp_cov auto -ins_length 300 -scaffolding no
```
Output: 
Final graph has 817 nodes and n50 of 16580, max 90662, total 5503767, using 906308/999998 reads

Output:
Final graph has 814 nodes and n50 of 15627, max 90352, total 5502284, using 906201/999998 reads

Posteriormente, creamos una carpeta para correr spades dentro de la carpeta de ENSAMBLE y activamos el entorno. 
```
mkdir SPADES
cd SPADES
conda activate spades
ln -s ../../DATA/ILLU_GRP4_1.fq
ln -s ../../DATA/ILLU_GRP4_2.fq
```

Spades se corrió con los siguientes comandos:
```
spades.py --careful -t 10 -1 ILLU_GRP4_1.fq -2 ILLU_GRP4_2.fq -k 21,33,55,77 -o SPADES_V1
spades.py --careful -t 10 -1 ILLU_GRP4_1.fq -2 ILLU_GRP4_2.fq -k 51,61,63,69,71 -o SPADES_V2
spades.py --careful -t 10 -1 ILLU_GRP4_1.fq -2 ILLU_GRP4_2.fq -k 17,23,31,41,53,67,79 -o SPADES_V3
```

Posterior al ensamblado con Spades, decidimos correr Unicycler con el archivo de PacBio, para esto se creo una carpeta dentro de ENSAMBLE:
```
mkdir UNICYCLER
conda activate unicycler
ln -s ../../DATA/PAC_GRP4.fastq 
```

Luego de esto, procedemos a correr unicycler con los siguientes comandos:
```
unicycler -t 10 --linear_seqs 1 -l PAC_GRP4.fastq -o UNI_LONG_V1
```

Ya una vez que tenemos los ensambles mediante tres programas distintos, procedemos a correr un quast para evaluar cual es el mejor
```
conda activate quast 
quast -m 200 VELVETH/VELVET_43/contigs.fa SPADES/SPADES_V1/contigs.fasta SPADES/SPADES_V2/contigs.fasta SPADES/SPADES_V3/contigs.fasta UNICYCLER/UNI_LONG_V1/assembly.fasta
```

Hasta el momento, de acuerdo a los resultados del quast, el mejor programa ha sido Unicycler en la version LONG, esto debido a que reduce drásticamente el número de contigs (10 frente a más de 100 en otros ensambladores), incrementa de manera significativa el valor de N50 (1.79 Mb) y genera los contigs más largos (2.1 Mb), manteniendo al mismo tiempo una longitud total coherente con el tamaño esperado del genoma (~5.7 Mb) y un contenido GC consistente (~67%).

De igual forma, procederemos a correr Unicycler pero en la version SHORT y HYBRID. 
```
cd UNICYCLER
ln -s ../../DATA/ILLU_GRP4_1.fq 
ln -s ../../DATA/ILLU_GRP4_2.fq
conda activate unicycler
unicycler -1 ILLU_GRP4_1.fq -2 ILLU_GRP4_2.fq -t 6 -o UNI_SHORT_V1
unicycler -t 10  --linear_seqs 1 -1 ILLU_GRP4_1.fq -2 ILLU_GRP4_2.fq -l PAC_GRP4.fastq   -o UNI_HYBRID_V1
```

Posterior a esto, correremos el programa Canu y Flye para poder comparar de nuevo nuestros resultados, los corrimos con los siguientes comandos:

```
cd ../
mkdir CANU
cd CANU
conda activate canu
ln -s ../../DATA/PAC_GRP4.fastq  
canu -d CANU_V1 -p GRP4 genomeSize=5.7m -pacbio PAC_GRP4.fastq
mkdir FLYE
cd FLYE
ln -s ../../DATA/PAC_GRP4.fastq 
conda deactivate
flye -g 5.7m -t 15 --pacbio-corr PAC_GRP4.fastq -o FLYE_V1
```
Luego de que estos dos programas terminen, pasamos a correr otro quast dentro de la carpeta ENSAMBLE para poder comparar el que nos salió mejor la vez pasada, con los programas más recientes.

```
conda activate quast
quast -m 200 UNICYCLER/UNI_LONG_V1/assembly.fasta UNICYCLER/UNI_SHORT_V1/assembly.fasta UNICYCLER/UNI_HYBRID_V1/assembly.fasta CANU/CANU_V1/GRP4.contigs.fasta FLYE/FLYE_V1/assembly.fasta
```

Posteriormente, correremos el programa mummer con los siguientes comandos, los resultados irán dentro de una carpeta llamada MUMMER que se encuentra dentro de ENSAMBLE::

```
mkdir MUMMER
cd MUMMER
mkdir MUMMER_CANU_FLYE
mkdir MUMMER_UNIHYB_CANU
cd MUMMER_CANU_FLYE/
ln -s ../../CANU/CANU_V1/GRP4.contigs.fasta
ln -s ../../FLYE/FLYE_V1/assembly.fasta
nucmer --coords GRP4.contigs.fasta assembly.fasta
mummerplot out.delta
cd ../MUMMER_UNIHYB_CANU/
ln -s ../../UNICYCLER/UNI_HYBRID_V1/assembly.fasta
ln -s ../../CANU/CANU_V1/GRP4.contigs.fasta
nucmer --coords assembly.fasta GRP4.contigs.fasta
mummerplot out.delta
```
