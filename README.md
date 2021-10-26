# hse21_hw1
###### Мозговой Владислав БМТ191

## Задание 1

#### 1. Создание ссылок в папке
```bash
 ls /usr/share/data-minor-bioinf/assembly/* | xargs -tI{} ln -s {}
```
#### 2. Выбор случайных чтений
```bash
SEED=127
seqtk sample -s$SEED oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s$SEED oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s$SEED oilMP_S4_L001_R1_001.fastq 1500000 > matep1.fastq
seqtk sample -s$SEED oilMP_S4_L001_R2_001.fastq 1500000 > matep2.fastq
```
#### 3. Оценка чтений используя FastQC
```bash
mkdir fastqc
ls sub* matep* | xargs -tI{} fastqc -o fastqc {}
```
#### 4. Создание отчета через MultiQC
```bash
mkdir multiqc
multiqc -o multiqc fastqc
```
#### 5. Обрезание чтений
```bash
platanus_trim sub*
platanus_internal_trim matep*
```
#### 6. Удаление изначальных файлов
```bash
rm sub* matep*
```
#### 7. Оценка качества обрезанных чтений используя FastQC
```bash
mkdir fastqc_trimmed
ls sub* matep*| xargs -tI{} fastqc -o fastqc_trimmed {}
```
#### 8. Создание отчета для обрезанных чтений через MultiQC
```bash
mkdir multiqc_trimmed
multiqc -o multiqc_trimmed fastqc_trimmed
```
#### 9. Сбор контиг используя “platanus assemble”
```bash
time platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
```
#### 10. Сбор скаффолдов используя “platanus scaffold”
```bash
time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matep1.fastq.int_trimmed matep2.fastq.int_trimmed 2> scaffold.log
```
#### 11. Уменьшение числа промежутков
```bash
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matep1.fastq.int_trimmed matep2.fastq.int_trimmed 2> gapclose.log
```

## Отчёты multiQC
#### Для исходных чтений
![](https://github.com/Vladm0z/hse21_hw1/blob/main/images/General_Statistics_1.png)
![](https://github.com/Vladm0z/hse21_hw1/blob/main/images/Per_Sequence_Quality_Scores_1.png)

#### Для подрезанных чтений
![](https://github.com/Vladm0z/hse21_hw1/blob/main/images/General_Statistics_2.png)
![](https://github.com/Vladm0z/hse21_hw1/blob/main/images/Per_Sequence_Quality_Scores_2.png)

## Jupiter Notebook
еще не рабочая ![ссылка]()

#### 1. Импорт библиотек
```bash
import re
```

#### 2. Функция для получения данных
```bashdef Get_Info(file):
    c = 0
    length = 0
    lines = []
    for line in file.readlines():
        if line[0] == '>':
            c += 1
            id_len = line.find('len')+3
            length += int(line[id_len : line.find('_', id_len)])
            lines.append(int(line[id_len : line.find('_', id_len)]))
    lines.sort(reverse = True)
    n50 = 0
    for i in lines:
        n50 += i
        if (n50 >= (length/2)):
            n50 = i
            break
    info = [c, length, lines[0], n50]
    return info
```

#### 3. Контиги
```bash
contig = open('Poil_contig.fa', 'r')
info = Get_Info(contig)
print(f'Общее количество контигов: {info[0]} \n\
Общая длина контигов: {info[1]} \n\
Длина самого длинного контига: {info[2]} \n\
N50: {info[3]}')
```
Общее количество контигов: 614 
Общая длина контигов: 3925550 
Длина самого длинного контига: 179307 
N50: 55043


#### 4. Скаффолды
```bash
scaffold = open('Poil_scaffold.fa', 'r')
info = Get_Info(scaffold)
print(f'Общее количество скаффолдов: {info[0]} \n\
Общая длина скаффолдов: {info[1]} \n\
Длина самого длинного скаффолда: {info[2]} \n\
N50: {info[3]}')
```
Общее количество скаффолдов: 73 
Общая длина скаффолдов: 3875965 
Длина самого длинного скаффолда: 3831671 
N50: 3831671

#### 5.
```bash

```
