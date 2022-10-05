# hse22_hw1
# Список, используемых команд
### 1.Создание символических ссылок на файлы

ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq

ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq

ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq

ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq

### 2. Выбираем образец 725 и случайно отбираем 5000000 чтений pair-end и 1500000 чтений mate pair

seqtk sample -s725 oil_R1.fastq 5000000 > subpr1.fastq

seqtk sample -s725 oil_R2.fastq 5000000 > subpr2.fastq

seqtk sample -s725 oilMP_S4_L001_R1_001.fastq 1500000 > mate2.fastq

seqtk sample -s725 oilMP_S4_L001_R2_001.fastq 1500000 > mate1.fastq

*для бонусной задачи я взяла 2500000 pair-end и 750000 mate-pairs

### 3. Оценка чтений используя FastQC

mkdir fastqc

ls subpr* mate* | xargs -tI{} fastqc -o fastqc {}

### 4. Создание отчета через MultiQC

mkdir multiqc

multiqc -o multiqc fastqc

### 5. Обрезание чтений

platanus_trim subpr*

platanus_internal_trim mate*

### 6. Удаление изначальных файлов

rm subpr1.fastq
mate1.fastq
mate2.fastq
subpr2.fastq

### 7. Оценка качества обрезанных чтений используя FastQC

mkdir fastqc_trimmed

ls subpr* mate*| xargs -tI{} fastqc -o fastqc_trimmed {}

### 8. Создание отчета для обрезанных чтений через MultiQC

mkdir multiqc_trimmed

multiqc -o multiqc_trimmed fastqc_trimmed

### 9. Сбор контигов используя platanus assemble

screen platanus assemble -o Poil -f subpr1.fastq.trimmed subpr2.fastq.trimmed 2> assemble.log

### 10. Сбор скаффолдов

platanus scaffold -o Poil -c Poil_contig.fa -IP1 subpr1.fastq.trimmed subpr2.fastq.trimmed -OP2 mate1.fastq.int_trimmed mate2.fastq.int_trimmed 2> scaffold.log

### 11. Уменьшение числа gap-ов

platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 subpr1.fastq.trimmed subpr2.fastq.trimmed -OP2 mate1.fastq.int_trimmed mate2.fastq.int_trimmed 2> gapclose.log

### 12. Удаление подрезанных чтений

rm subpr*.trimmed mate*.int_trimmed

# Отчеты MultiQC

## Общая статистика

### полные данные 
<img width="924" alt="Снимок экрана 2022-10-04 в 20 48 16" src="https://user-images.githubusercontent.com/99287058/193890102-420b3a52-cf19-4e5a-81c9-f1ad64088766.png">

### подрезанные данные
<img width="928" alt="Снимок экрана 2022-10-04 в 20 48 47" src="https://user-images.githubusercontent.com/99287058/193890184-223a1cf7-4ebd-49d3-aeb8-07fee03a2171.png">

## Sequence Quality Histograms

### полные данные 
<img width="925" alt="Снимок экрана 2022-10-04 в 20 52 50" src="https://user-images.githubusercontent.com/99287058/193890924-2e0c48ee-74ce-4db5-8367-8d72a18d212a.png">

### подрезанные данные
<img width="926" alt="Снимок экрана 2022-10-04 в 20 53 17" src="https://user-images.githubusercontent.com/99287058/193891005-d8571f22-3bf1-4621-8163-7e2fe6429f41.png">

## Adapter Content

### полные данные 
<img width="926" alt="Снимок экрана 2022-10-04 в 20 54 34" src="https://user-images.githubusercontent.com/99287058/193891264-6a884c36-c0c9-4ded-894a-26a4c788e02a.png">

 ### подрезанные данные
 <img width="923" alt="Снимок экрана 2022-10-04 в 20 55 42" src="https://user-images.githubusercontent.com/99287058/193891497-a9288c0d-490c-46e5-95d6-64a4cf75d872.png">

## Status Checks

### полные данные 
<img width="926" alt="Снимок экрана 2022-10-04 в 20 56 41" src="https://user-images.githubusercontent.com/99287058/193891686-977ba28c-7333-41d3-9ab1-0c87b0eb64a2.png">

### подрезанные данные
<img width="922" alt="Снимок экрана 2022-10-04 в 20 57 24" src="https://user-images.githubusercontent.com/99287058/193891828-6bbc6c69-ecfa-4b9d-a427-b80d245328e9.png">

# Анализ контигов и скаффолдов

осуществлялся с помощью [кода](https://colab.research.google.com/drive/17pLCzigRx4xlAS_6ionh1Qqnd_hvIdnu#scrollTo=HwAVeVS66j40)

# Результаты

## Контиги

<img width="244" alt="Снимок экрана 2022-10-05 в 15 06 10" src="https://user-images.githubusercontent.com/99287058/194056525-0c8c900f-6eab-49b2-9186-9cb9b098ee3a.png">

## Скаффолды

<img width="278" alt="Снимок экрана 2022-10-05 в 15 06 49" src="https://user-images.githubusercontent.com/99287058/194056622-9a7d3075-372f-4e17-b240-bd3b1015a72c.png">

## Скаффолды после gapClose

<img width="256" alt="Снимок экрана 2022-10-05 в 15 07 33" src="https://user-images.githubusercontent.com/99287058/194056735-4357f629-1a54-4caf-a763-6aa20bb5a637.png">

# Возьмем меньшее количество чтений:
### Для pair-end 2.500.000 ридов, а для mate-pairs 750.000

## Контиги
<img width="268" alt="Снимок экрана 2022-10-05 в 15 08 55" src="https://user-images.githubusercontent.com/99287058/194056937-5be49443-ecae-4e63-aa16-6fe8b6de18be.png">

## Скаффолды

<img width="258" alt="Снимок экрана 2022-10-05 в 15 09 12" src="https://user-images.githubusercontent.com/99287058/194056985-3f860aa7-75b0-48e0-bc39-95fe5900d0e9.png">

## Скаффолды после gapClose

<img width="259" alt="Снимок экрана 2022-10-05 в 15 09 34" src="https://user-images.githubusercontent.com/99287058/194057051-8a2cb688-1449-4b20-9634-f7e4a0f14770.png">

## Вывод
Контигов стало больше, но их общая длина чуть убавилась, а также повысился N50 

Скаффолдов (до gapclose) тоже стало больше, их общая длина стала меньше, как и N50; количество и общая длина гэпов особо не изменились. 

Однако показатели для скаффолдов после gapClose одинаковы и для исходного, и для уменьшенного в два раза количества ридов.
