Django projekt na k8s
=====

.. _installation:

Nastavení repozitáře
--------------------

Zaprvé, je potřeba mít funkční projekt v Djangu. Jestliže takový projekt nemáte, můžete si stáhnout ukázkový projekt pomocí nasledujících příkazů.

.. code-block:: console
   git clone https://github.com/lucny/mat_filmy.git
   cd mat_filmy
   git checkout solution

Následně budete muset vytvořit soubor ``Dockerfile`` v kořenovém adresáři projektu. Můžete použít následující kód.

.. code-block:: dockerfile

    FROM python:3.11

    WORKDIR /app
    
    COPY . /app
    
    RUN pip install --no-cache-dir -r requirements.txt
    
    EXPOSE 8000
    
    # 0.0.0.0:8000 zajistí, že server bude naslouchat na všech zařízeních, což je pro chod v containeru nutné
    CMD [ "python", "manage.py", "runserver", "0.0.0.0:8000" ] 

Tvorba image pomocí GitHub Actions
----------------------------------



>>> import lumache
>>> lumache.get_random_ingredients()
['shells', 'gorgonzola', 'parsley']

