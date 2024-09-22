Django projekt na k8s s MariaDB
===============================

.. _setup:

.. note::

   Tento návod počítá se schopností deploymentu Django aplikace bez externí databáze.

.. warning::
   
   Tento návod je aktuálně ve vývoji a může obsahovat chyby.

Nastavení repozitáře
--------------------

Nainstaluje ``mysqlclient`` a aktualizujte soubor ``requirements.txt``.

.. code-block:: console

   pip install mysqlclient
   pip freeze > requirements.txt

V souboru settings.py v sekci ``DATABASES`` nastavte připojení k databázi. Můžete použít následující kód.

.. code-block:: python

   DATABASES = {
       "default": {
           "ENGINE": "django.db.backends.mysql",
           "NAME": "mat_filmy",
           "USER": "mat_filmy_user",
           "PASSWORD": "mat_filmy_password",
           "HOST": "db",
           "PORT": 3306,
       }
   }

Nastavení v Rancheru
--------------------

Do školního Rancheru proveďte deployment aplikace dle předchozího návodu. 

Následně v sekci ``Workloads`` začněne vytvářet nový ``StatefulSet`` s názvem ``db``. Jako ``Image`` zadejte ``mariadb:11.5``. V sekci ``Environment`` nastavte proměnnou ``MARIADB_RANDOM_ROOT_PASSWORD`` na ``true``, ``MARIADB_DATABASE`` na ``mat_filmy``, ``MARIADB_USER`` na ``mat_filmy_user`` a ``MYSQL_PASSWORD`` na ``mat_filmy_password``. Zatím **neklikněte** na tlačítko ``Create``.

Přepněte do sekce ``Pod`` a tam do podsekce ``Storage``.

Klikněte na ``Add volume`` a klikněte na ``Create new persistent volume claim``. Jako název zadejte ``db-data``, jakožto ``Access Mode`` vyberte ``Single-Node Read/Write`` a jako velikost zadejte ``1``.

Přepněte zpět do sekce ``container-0`` a v podsekci ``Storage`` vyberte vytvořený ``Persistent Volume Claim``. Jakožto ``Mount Path`` zadejte ``/var/lib/mysql``.

V sekci ``Networking`` nastavte ``Service`` na ``ClusterIP``, jakožto ``Name`` zadejte ``db`` a jako ``Port`` zadejte ``3306``.

Nyní můžete kliknout na tlačítko ``Create``.


