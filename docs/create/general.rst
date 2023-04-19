Создание датасета
+++++++++++++++++

Классификация изображений
=========================
Для создания датасета для решения задачи классификации изображений необходимо создать экземпляр класса ``ImageClassification`` со следующими параметрами:

- source_path: List[str] - путь к общей папке или список путей к папкам с изображениями;
- width: int - ширина изображения;
- height: int - высота изображения;
- preprocessing: str - скейлер: ``None``, ``MinMaxScaler``, ``TerraImageScaler``;
- network: str - тип нейросети: ``Convolutional`` (для сверточной), ``Linear`` (для линейной);
- process: str - тип обработки изображений: ``Fit``, ``Stretch``, ``Cut``;
- train_size: float - размер обучающей выборки от 0 до 1;
- one_hot_encoding: bool - перевод меток класса (output) в формат One-Hot Encoding.


.. code-block:: python

    from terra_ai_datasets.create import ImageClassification

    img_cls_dataset = ImageClassification(
        source_path=['./папка'],
        width=160,
        height=128,
        preprocessing="None",
        network="Convolutional",
        process="Fit",
        train_size=0.7,
        one_hot_encoding=True
    )
..


Классификация текстов
=====================
Для создания датасета для решения задачи классификации текстов необходимо создать экземпляр класса ``TextClassification`` со следующими параметрами:

- source_path: List[str] - путь к общей папке или список путей к папкам с текстовыми файлами;
- filters: str - символы, подлежащие удалению: –—!"#$%&()*+,-./:;<=>?@[\\]^«»№_`{|}~\t\n\xa0–\ufeff
- preprocessing: str - метод обработки: ``None``, ``Embedding``, ``Bag of words``, ``Word2Vec``;
- max_words_count: int - *опционально при preprocessing in* [``Embedding``, ``Bag of words``]  - максимальное количество слов в словаре частотности (Tokenizer);
- word2vec_size: int - *опционально при preprocessing ==* ``Word2Vec``  - размер векторного пространства для каждого слова;
- mode: str - режим обработки текстов: ``Full``, ``Length and step``;
- max_words: int - *опционально при mode==* ``Full`` - максимальное количество слов из одного текстового файла;
- length: int - *опционально при mode==* ``Length and step`` - количество слов в одном примере;
- step: int - *опционально при mode==* ``Length and step`` - шаг окна разбивки текстов;
- pymorphy: bool - приведение слов к нормальной форме (значительно увеличивает время обработки);
- train_size: float - размер обучающей выборки от 0 до 1;
- one_hot_encoding: bool - перевод меток класса (output) в формат One-Hot Encoding.


.. code-block:: python

    from terra_ai_datasets.create import TextClassification

    text_cls_dataset = TextClassification(
        source_path=["./папка/подпапка_1", "./папка/подпапка_2"],
        max_words_count=20000,
        mode="Length and step",
        preprocessing="Embedding",
        length=2000,
        step=200,
        pymorphy=False
        train_size=0.7,
        one_hot_encoding=True,
    )
..


Классификация аудио
===================
Для создания датасета для решения задачи классификации аудио необходимо создать экземпляр класса ``AudioClassification`` со следующими параметрами:

- source_path: List[str] - путь к общей папке или список путей к папкам с аудиофайлами;
- sample_rate: int - частота дискретизации;
- mode: str - режим обработки аудио: ``Full``, ``Length and step``;
- parameter: List[str] - список доступных параметров для предобработки (для каждого параметра будет создан отдельный вход): ``Audio signal``, ``Chroma STFT``, ``MFCC``, ``RMS``, ``Spectral centroid``, ``Spectral bandwidth``, ``Spectral roll-off``, ``Zero-crossing rate``;
- max_seconds: int - *опционально при mode==* ``Full`` - длительность сегмента в сек.;
- length: int - *опционально при mode==* ``Length and step`` - длительность сегмента в сек.;
- step: int - *опционально при mode==* ``Length and step`` - длительность шага окна разбивки в сек.;
- fill_mode: str - способ заполнения при условии, что аудиофайл или отрезок оказался короче чем length или max_seconds: ``Last millisecond``, ``Loop``;
- resample: str - тип ресемпла при открытии файлов: ``Kaiser best``, ``Kaiser fast``, ``Scipy``;
- preprocessing: str - скейлер: ``None``, ``MinMaxScaler``, ``StandardScaler``;
- train_size: float - размер обучающей выборки от 0 до 1;
- one_hot_encoding: bool - перевод меток класса (output) в формат One-Hot Encoding.


.. code-block:: python

    from terra_ai_datasets.create import AudioClassification

    aud_cls_dataset = AudioClassification(
        source_path=['./папка/подпапка_1', './папка/подпапка_2'],
        sample_rate=22050,
        mode="Length and step",
        parameter=["Audio signal", "MFCC", "RMS"],
        fill_mode="Last millisecond",
        resample="Scipy",
        preprocessing="None",
        length=0.5,
        step=0.4,
        train_size=0.7,
        one_hot_encoding=True
        )
..

Классификация табличных данных
==============================
Для создания датасета для решения задачи классификации, используя табличные данные, необходимо создать экземпляр класса ``DataframeClassification`` со следующими параметрами:

- csv_path: str - путь до csv-файла;
- inputs: List[dict] - список входов;
- output: str - колонка с числовыми данными, которые необходимо предсказать;
- one_hot_encoding: bool - перевод меток класса (output) в формат One-Hot Encoding.
- train_size: float - размер обучающей выборки от 0 до 1;

Список доступных типов данных, используемых в качестве входа: ``Image``, ``Text``, ``Audio``, ``Categorical``, ``Raw``.

.. note::
    При использовании во входе типов данных ``Text`` или ``Audio`` выбирайте ``mode="Full"``


.. code-block:: python

    from terra_ai_datasets.create import DataframeRegression

    df_regr_dataset = DataframeRegression(
        csv_path='папка/пример.csv',
        inputs=[
            {"columns": ["Колонка_1", "Колонка_2"], "type": "Categorical", "parameters": {"one_hot_encoding": True}},
            {"columns": ["Колонка_3"], "type": "Text", "parameters": {"max_words_count": 10000, "mode": "Full", "preprocessing": "Embedding", "max_words": 30, "pymorphy": False}},
        ],
        output="Колонка_5",
        one_hot_encoding=True,
        train_size=0.7
    )
..

Регрессия табличных данных
==========================

Для создания датасета для решения задачи регрессии необходимо создать экземпляр класса ``DataframeRegression`` со следующими параметрами:

- csv_path: str - путь до csv-файла;
- inputs: List[dict] - список входов;
- output: str - колонка с числовыми данными, которые необходимо предсказать;
- preprocessing: str - скейлер (выход): ``None``, ``MinMaxScaler``, ``StandardScaler``;
- train_size: float - размер обучающей выборки от 0 до 1;

Список доступных типов данных, используемых в качестве входа: ``Image``, ``Text``, ``Audio``, ``Categorical``, ``Raw``.

.. note::
    При использовании во входе типов данных ``Text`` или ``Audio`` выбирайте ``mode="Full"``


.. code-block:: python

    from terra_ai_datasets.create import DataframeRegression

    df_regr_dataset = DataframeRegression(
        csv_path='папка/пример.csv',
        inputs=[
            {"columns": ["Колонка_1", "Колонка_2"], "type": "Categorical", "parameters": {"one_hot_encoding": True}},
            {"columns": ["Колонка_3"], "type": "Text", "parameters": {"max_words_count": 10000, "mode": "Full", "preprocessing": "Embedding", "max_words": 30, "pymorphy": False}},
        ],
        output="Колонка_4",
        preprocessing="StandardScaler",
        train_size=0.7
    )
..


Сегментация изображений
=======================
Для создания датасета для решения задачи сегментации изображений необходимо создать экземпляр класса ``ImageSegmentation`` со следующими параметрами:

- source_path: List[str] - путь к общей папке или список путей к папкам с изображениями;
- target_path: List[str] - путь к общей папке или список путей к папкам с масками сегментации;
- width: int - ширина изображения;
- height: int - высота изображения;
- preprocessing: str - скейлер: ``None``, ``MinMaxScaler``, ``TerraImageScaler``;
- network: str - тип нейросети: ``Convolutional`` (для сверточной), ``Linear`` (для линейной);
- process: str - тип обработки изображений: ``Fit``, ``Stretch``, ``Cut``;
- rgb_range: int - допустимое отклонение значений RGB, при котором пиксель будет присвоен ближайшему классу;
- classes: Dict[str, List[int]] - *опционально* - ручной ввод названий классов и их цветов в формате RGB;
- num_classes: int - *опционально* - количество классов в масках сегментации, которые будет искать автоматический поиск;
- classes_path: str - *опционально* - путь к файлу аннотации;
- train_size: float - размер обучающей выборки от 0 до 1;

.. note::
    Для создания датасета необходимо указать хотя бы один из опциональных параметров. В случае одновременного указания двух и более параметров, очередность приоритетов будет следующей: ``classes``, ``classes_path``, ``num_classes``.


.. code-block:: python

    from terra_ai_datasets.create import ImageSegmentation

    img_seg_dataset = ImageSegmentation(
        source_path=['./папка/изображения'],
        target_path=['./папка/маски'],
        width=160,
        height=128,
        preprocessing="MinMaxScaler",
        network="Convolutional",
        process='Fit',
        rgb_range=50,
        classes={"класс_1": [255, 255, 255], "класс_2": [0, 0, 0]}
        num_classes=2,
        classes_path="./папка/labelmap.txt"
        train_size=0.7,
    )
..

Временные ряды
==============
Для создания датасета для решения задачи предсказания временных рядов необходимо создать экземпляр класса ``TimeseriesDepth`` со следующими параметрами:

- csv_path: str - путь до csv-файла;
- inputs: List[str] - список колонок, используемых в качестве входа;
- outputs: List[str] - список колонок, используемых в качестве выхода;
- preprocessing: str - скейлер: ``None``, ``MinMaxScaler``, ``StandardScaler``;
- length: int - количество значений временного яда, на основе которых будет происходить предсказание;
- step: int - длин шага сдвига окна разбивки временного ряда при составлении примеров;
- depth: int - глубина предсказания временного ряда;
- train_size: float - размер обучающей выборки от 0 до 1;

.. code-block:: python

    ts_depth = TimeseriesDepth(
        csv_path="папка/пример.csv",
        inputs=["<OPEN>", "<HIGH>", "<LOW>", "<CLOSE>"],
        outputs=["<CLOSE>"],
        preprocessing="StandardScaler",
        length=100,
        step=20,
        depth=30,
        train_size=0.7,
    )
..

Тренд временных рядов
=====================
Для создания датасета для решения задачи предсказания тренда временных рядов необходимо создать экземпляр класса ``TimeseriesTrend`` со следующими параметрами:

- csv_path: str - путь до csv-файла;
- inputs: List[str] - список колонок, используемых в качестве входа;
- outputs: List[str] - список колонок, используемых в качестве выхода;
- preprocessing: str - скейлер: ``None``, ``MinMaxScaler``, ``StandardScaler``;
- length: int - количество значений временного яда, на основе которых будет происходить предсказание;
- step: int - длин шага сдвига окна разбивки временного ряда при составлении примеров;
- deviation: int - отклонение нулевого тренда в процентах;
- train_size: float - размер обучающей выборки от 0 до 1;
- one_hot_encoding: bool - перевод меток класса (output) в формат One-Hot Encoding.

.. code-block:: python

    ts_trend = TimeseriesTrend(
        csv_path="папка/пример.csv",
        inputs=["<OPEN>", "<HIGH>", "<LOW>", "<CLOSE>"],
        outputs=["<CLOSE>"],
        preprocessing="StandardScaler",
        length=100,
        step=20,
        deviation=0.5,
        one_hot_encoding=True
        train_size=0.7,
    )
..