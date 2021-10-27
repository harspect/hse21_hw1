# ДЗ №1 
### Поморцев Леонид 2 группа
Создаем директорию
```
mkdir hw
cd hw
mkdir first
cd first
```
Создаем ссылки на данные
```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq  
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq  
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq  
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
```
Отбираем случайные чтения 
```
SEED=124
seqtk sample -s$SEED oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s$SEED oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s$SEED oilMP_S4_L001_R1_001.fastq 1500000 > mp1.fastq
seqtk sample -s$SEED oilMP_S4_L001_R2_001.fastq 1500000 > mp2.fastq
```
Делаем удобную функцию
```
Dofastqc() {
  rm -rf fastqc multiqc
  mkdir fastqc multiqc
  fastqc "$@" -o fastqc
  multiqc fastqc -o multiqc
}
```
Проверяем качество
```
Dofastqc mp*.fastq sub*.fastq
```
Подрезаем
```
platanus_trim sub*.fastq
platanus_internal_trim mp*.fastq
```
Удаляем ненужное
```
rm mp*.fastq           
rm sub*.fastq
```
Проверяем качество для подрезанных
```
Dofastqc *trimmed
```
Соберём контиги, скаффолды:
```
platanus assemble -f *.trimmed
platanus scaffold -c out_contig.fa -IP1 *.trimmed -OP2 *.int_trimmed
platanus gap_close -c out_scaffold.fa -IP1 *.trimmed -OP2 *.int_trimmed
rm *trimmed
```
Возьмем самый длинный в файл longest
```
sed -n '1,/^>/p' scaffold.fasta | head -n -1 > longest.fasta
```
## Статистика multiqc
### Для обычных
Ссылка на отчет: https://github.com/harspect/hse21_hw1/blob/main/multiqc_report_simple.html
![](images/1-1.png)
![](images/1-2.png)
### Для trimmed чтений
Ссылка на отчет: https://github.com/harspect/hse21_hw1/blob/main/multiqc_report_trimmed.html
![](images/2-1.png)
![](images/2-2.png)
### Код из Jupyter notebook
![](images/3-1.png)
![](images/3-2.png)
