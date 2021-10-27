# hse21_hw1
Команды первой части
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
Fastqc() {
  rm -rf fastqc multiqc
  mkdir fastqc multiqc
  fastqc "$@" -o fastqc
  multiqc fastqc -o multiqc
}
```
Проверяем качество
```
Fastqc mp*.fastq sub*.fastq
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
Fastqc *trimmed
```
Соберём контиги, скаффолды и скаффолды:
```
platanus assemble -f *.trimmed
platanus scaffold -c out_contig.fa -IP1 *.trimmed -OP2 *.int_trimmed
platanus gap_close -c out_scaffold.fa -IP1 *.trimmed -OP2 *.int_trimmed
rm *trimmed
```
Возьмем самый длинный
```
sed -n '1,/^>/p' scaff.fasta | head -n -1 >long.fasta
```
