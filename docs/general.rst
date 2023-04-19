Общие сведения
++++++++++++++
Terra AI Datasets - фреймворк для создания датасетов под целый ряд задач для дальнейшего использования в процессе обучения нейросетей в Keras.

Создание датасета
=====================

В разделе :ref:`Создание датасетов <create>` есть подробное описание каждых параметров и пример создания датасета под каждую из доступных задач:

- :ref:`Классификация изображений <image_classification>`
- :ref:`Классификация текстов <text_classification>`
- :ref:`Классификация аудио <audio_classification>`
- :ref:`Классификация табличных данных <table_classification>`
- :ref:`Регрессия табличных данных <table_regression>`
- :ref:`Сегментация изображений <image_segmentation>`
- :ref:`Временные ряды <timeseries_depth>`
- :ref:`Тренд временных рядов <timeseries_trend>`


Пример создания датасета для решения задачи классификации изображений:

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

Информация о датасете
=====================
Для отображения информации о датасете, включая первые 5 примеров, а также количество примеров в обучающей и валидационной выборках, можно воспользоваться методом ``.summary()``

.. code-block:: python

    img_cls_dataset.summary()
..

При создании датасета для задачи классификации дополнительно будут выведены количество примеров для каждого из классов по каждой из выборок.

Визуализация датасета
=====================
Для визуализации случайного примера из датасета можно воспользоваться методом ``.visualize()``

.. code-block:: python

    img_cls_dataset.visualize()
..

.. note::
    Визуализация датасета доступна только для задач :ref:`Классификации изображений <image_classification>`, :ref:`Сегментации изображений <image_segmentation>`, :ref:`Временных рядов <timeseries_depth>`.


Формирование датасета
=====================
Перед началом обучения нейросети необходимо запустить метод ``.create(use_generator: bool = False)``

В процессе создания датасета происходит не только формирование массивов, но и обучение препроцессингов (MinMaxScaler, StandardScaler, TerraImageScaler, Tokenizer).

При ``.create(use_generator=True)`` будут созданы только массивы для тренировочной выборки только в целях обучения объектов препроцессинга. После этого массивы будут удалены из памяти.

.. note::
    Использование генератора замедляет процесс обучения нейросети, так как каждый пример будет сгенерирован заново.


Обучение нейросети
==================

В процессе формирования датасета был создан итерируемый объект tf.data.Dataset.
Его преимущество в том, что он подходит как для выдачи готовых массивов, так и для генераторов.
Это означает, что код обучения нейросети как на готовых массивах, так и на генераторе будет выглядеть одинаково.

.. code-block:: python

    from tensorflow.keras.models import Sequential
    from tensorflow.keras.layers import Input, Conv2D, MaxPooling2D, Dropout, Flatten, Dense

    model = Sequential()
    model.add(Input(shape=(128, 160, 3), name="input_1"))
    model.add(Conv2D(32, (3, 3), padding='same', activation='relu'))
    model.add(Conv2D(32, (3, 3), padding='same', activation='relu'))
    model.add(MaxPooling2D(pool_size=(2,2)))
    model.add(Dropout(0.25))
    model.add(Flatten())
    model.add(Dense(64, activation='relu'))
    model.add(Dropout(0.25))
    model.add(Dense(3, activation='softmax', name="output_1"))
    model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])

    model.fit(
        img_cls_dataset.dataset['train'].batch(64),
        epochs=15,
        validation_data=img_cls_dataset.dataset['val'].batch(64),
    )
..

.. warning::
    Обратите внимание, что необходимо вручную задать названия входным и выходным слоям нейросети в следующем виде: ``input_*``, ``output_*``


Предсказание
============

Для получения предсказания на валидационной выборке можно воспользоваться методом ``.evaluate_on_model(model, batch_size: int)``

Метод возвращает:

- входные данные;
- истинное значение;
- предсказанное значение.

.. note::
    Данный метод является генератором, поэтому для предсказания на всей валидационной выборке следует итерировать циклом ``for``.

.. code-block:: python

    inp, out, pred = next(img_cls_dataset.evaluate_on_model(model, batch_size=num_samples))
..
