# ДЗ по метилированию ДНК
## Ссылка на Коллаб:

https://colab.research.google.com/drive/1m_QZF9xaiZzVppYig7yKCXKshHri0IXi?usp=sharing

## 1. Анализ QC прочтений
Я выбрал образец из стадии 8 клеток. Отчёт загружен в репозиторию. Анализ Per sequence GC content показывает нам, что GC-нуклеотиды преобладают над остальными. А из википедии (статьи) нам известно, что для стадии 8 клеток уровень метилирования увеличивается, то есть данные не противоречат (%GC = 36). Можно предположить, что %GC будет ниже, чем при секвенировании, например, РНК, так как часть цитозинов (а именно неметилированные цитозины) в ходе бисульфидного секвинирования заменяется на урацилы.

![image](https://user-images.githubusercontent.com/93254228/154434313-2c740a8c-8627-49ba-8e35-6ea6d3550a09.png)
![image](https://user-images.githubusercontent.com/93254228/154434385-6a4bffd0-2b7b-46cf-ba67-12a8fcd320b4.png)

## 2. Дальнейшая работа
### a) Картирование на заданные участки

![image](https://user-images.githubusercontent.com/93254228/154446119-5f96a7a6-9bcb-4dad-b9fe-3fadf7f62b5a.png)

### b) Дедупликация

![image](https://user-images.githubusercontent.com/93254228/154454832-6e5a9775-0337-4617-9e0e-528f08b2f281.png)

Бонус: одновременная дедупликация через скрипт: `! ls *pe.bam | xargs -P 4 -tI{} deduplicate_bismark  --bam  --paired  -o s_{} {}`

### c) Коллинг метилирования цитозинов 
Колинг был выполнен, все отчёты прилагаются.
### d) M-bias plots
Графики, описывающие метилирование в образцах (расположены по убыванию процента CpG-метилирования).

Epiblast:

![Bismark M-bias Read 1](https://user-images.githubusercontent.com/93254228/154489509-33eee5ac-a04d-491f-bc51-b03d466abcd3.png)

![Bismark M-bias Read 2](https://user-images.githubusercontent.com/93254228/154489542-35f13972-4e51-4ada-9208-e825f9dbf2fd.png)

8 cells:

![Bismark M-bias Read 1 (1)](https://user-images.githubusercontent.com/93254228/154489764-6fa077a4-c25a-49ae-b2a7-bf549abb438f.png)

![Bismark M-bias Read 2 (1)](https://user-images.githubusercontent.com/93254228/154489802-2b82f86e-1c80-45a9-b332-d3c840107658.png)

ICM:

![Bismark M-bias Read 1 (2)](https://user-images.githubusercontent.com/93254228/154489918-b57c0ee7-23a1-40ce-a1c8-c9552470d0d1.png)

![Bismark M-bias Read 2 (2)](https://user-images.githubusercontent.com/93254228/154489951-ecd35624-963c-4963-958b-5142544cb095.png)

Для наглядного объяснения пригодится график, показывающий уровень CpG-метилирования на разных стадиях развития:

![image](https://user-images.githubusercontent.com/93254228/154490202-7377343b-742d-46ef-9a08-09e28d6023c6.png)

Так, мы можем видеть, что на стадии Epiblast (6.5 дней) уровень метилирования самый высокий (в основном 77-78%), на стадии 8 cells (2.25 дня) метилирования меньше (42-45%), а самый низкий уровень метилирования (в основном 22-24%) наблюдается на промежуточной стадии ICM (3.5 дня), что соответствует графику.

### e) Гистограмма распределения метилирования цитозинов по хромосоме (BedGraph)
Для получения гистрограмм использовался данный код (на примере образца Epiblast):

`path = 's_epiblast.deduplicated.bedGraph'`

`graph = pd.read_csv(path,  delimiter='\t', skiprows=1, header=None)`

`plt.figure(figsize=(12, 7))`

`plt.title('Destribution of methylation in Epiblast', fontsize=18)`

`plt.hist(graph[3], bins=100, density=True)`

`plt.xlabel('Methylated cytosines, %')`

`plt.ylabel('Frequency')`

`plt.show()`

Предварительно нужно было скачать пару библиотек:

`import pandas as pd`

`from matplotlib import pyplot as plt`

![image](https://user-images.githubusercontent.com/93254228/154538435-52c9f3b4-e3c2-4190-8426-d5cf266776a0.png)

![image](https://user-images.githubusercontent.com/93254228/154538491-3514d036-6263-42a9-8280-eeb5fae74bae.png)

![image](https://user-images.githubusercontent.com/93254228/154538529-772e2879-12b7-471f-ac71-638baa9cc63d.png)

Анализ гистрограмм приводит нас к выводам, похожим на те, что мы сделали в пункте d. Уровень метилирования минимален для образца 'ICM', так как процент 0 встречается с частотой 0.6; максимальное метилирование мы наблюдаем в образце 'Epiblast', где процент 100 встречается с частотой, близкой к 0.5; Нечто среднее мы наблюдаем в образце '8 cells', где процент 0 встречается с частотой 0.5, а процент 100 - с частотой около 0.25.

### f) Визуализация уровней метилирования и покрытия (pyGenomeTracks)
Уровень метилирования:

![image_meth](https://user-images.githubusercontent.com/93254228/154507651-2a10484b-2957-4b62-8d9b-478590c7c0d5.png)

Уровень покрытия:

![image_cov](https://user-images.githubusercontent.com/93254228/154507717-05423f14-2da4-4458-9766-6d99e7eaead8.png)

