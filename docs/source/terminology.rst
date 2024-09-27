Kubernetes terminologie
***********************

V tomto dokumentu lze nálezt základní terminologii, která je používána v Kubernetes a která může být v ostatních návodech.

Každý objekt v Kubernetes je reprezentován v JSON nebo YAML formátu, ale pomocí Rancheru lze využít menu.

Základní pojmy
==============

Kubernetes
----------

Open-source platforma pro orchestraci kontejnerů. Kubernetes zajišťuje, že se kontejnery budou spouštět, budou dostupné a budou škálovat. Také označován jako k8s. Zjednodušeně řečeno, "velký" docker.

Node
----

Fyzický nebo virtuální server, který běží v Kubernetes clusteru. Každý node má svůj vlastní Kubelet, který komunikuje s API serverem.

Kubelet
-------

Agent, který běží na každém nodu a komunikuje s API serverem. Kubelet zajišťuje, že pody běží na nodu.

API Server
----------

Server, který zajišťuje komunikaci mezi uživatelem a clusterem. API server zpracovává požadavky uživatele a zajišťuje, že se změny provedou v clusteru.

Školní Kubernetes API server dostupný přes Rancher (``Download KubeConfig``).

Controller Manager
------------------

Komponenta, která zajišťuje, že se stav clusteru bude shodovat s požadavky uživatele. Controller manager zajišťuje, že se pody budou spouštět, že se budou vytvářet zdroje, atd.

Etcd
----

Databáze, která ukládá stav clusteru. Etcd je distribuovaná databáze, která zajišťuje, že se stav clusteru bude shodovat.

Cluster
-------

Skupina nodů, které spolu komunikují a sdílejí zdroje. Každý cluster má svůj vlastní API server, který slouží k řízení clusteru.

Aplikace
========

Namespace
---------

Logický oddělený prostor, který slouží k oddělení aplikací. Každý namespace má své vlastní zdroje, jako jsou pod, deploymenty, atd.

Container 
---------

Standardizovaný způsob balení aplikací a všech jejich závislostí do jednoho balíčku. 

Pod
---

Nejmenší jednotka v Kubernetes, která obsahuje jeden nebo více containerů. Tyto containery odkazují pomocí localhost na stejnou adresu - sdílí síť. Nejčastěji se používá jeden container na jeden pod.

Deployment 
----------
Objekt, který definuje stav aplikace a způsob, jakým se má aplikace nasadit a udržovat - lze například definovat počet replik podu pro high availability (ve škole limitováno na maximálně 3 repliky). Užitečné pro nasazení normálních stateless aplikací.

Jednotlivé části aplikace je doporučeno dávat do samostatných Deploymentů nebo StatefulSetů - umožňuje to lepší škálování.

StatefulSet
-----------

Objekt podobný deploymentu, ale je určen pro aplikace, které potřebují udržovat stav s tím, že při využití PVC vytváří vlastní PV. Užitečné pro aplikace, které potřebují udržovat stav, například databáze.

DaemonSet
---------

Objekt, který zajišťuje, že daný pod bude spuštěn na každém nodu v clusteru. Užitečné pro monitoringové agenty, logovací agenty, atd.

Job
---
Objekt, který zajišťuje, že daný pod bude spuštěn až do úspěšného dokončení. Užitečné pro jednorázové úlohy, například migrace dat.

CronJob
-------

Objekt, který zajišťuje, že daný pod bude vždy spuštěn v určitý čas. Užitečné pro pravidelné úlohy, například zálohování nebo cleanup.

HorizaontalPodAutoscaler
------------------------
Objekt, který zajišťuje, že se počet replik podu mění podle zátěže. Užitečné pro aplikace, které mají proměnnou zátěž. Jakožto metriku lze použít CPU nebo paměť.


Perzistentní úložiště
=====================

PV
--

Persistent volume = objekt, který reprezentuje fyzické úložiště, které může být připojeno k podu.

PVC 
---

Persistent volume claim = objekt pomocí kterého si pod může zažádat o připojení k PV (případně vytvoření PV). Každý má ``AccessMode``, ``StorageClass`` a ``Storage``.

``Access mode`` - režim, který určuje, jakým způsobem může být PV připojen k podu. Existují tři režimy: ``ReadWriteOnce``, ``ReadOnlyMany`` a ``ReadWriteMany``.

  - ``ReadWriteOnce`` - PV může být připojen pouze na jednom určitém nodu pro čtení a zápis.

  - ``ReadOnlyMany`` - PV může být připojen na více nodů pro čtení.

  - ``ReadWriteMany`` - PV může být připojen na více nodů pro čtení a zápis.

.. note::

  Ve školním clusteru je podporován pouze Access mode ``ReadWriteOnce``.

``StorageClass`` - třída, která definuje, jakým způsobem bude vytvořen PV. Ve školním clusteru je dostupná pouze třída ``local-path``.

``Storage`` - velikost úložiště, které si pod žádá.

ConfigMap
---------

Objekt, který obsahuje konfigurační data, která mohou být použita v podu. Užitečné pro konfiguraci aplikací.

Secret
------

Objekt, který obsahuje citlivá data, jako jsou hesla, klíče, atd. Užitečné pro ukládání citlivých dat. Data jsou standardně zakódována pomocí base64.


Přístup k aplikacím
===================

Service
-------
Definuje službu (port), kterou objekt exportuje. Název této service je možné použít jako DNS jméno pro přístup k aplikaci z jiných podů v rámci namespace.

Má několik módů:

- ``ClusterIP`` - služba je dostupná pouze v rámci clusteru
- ``NodePort`` - služba je dostupná na všech nodách v clusteru na daném portu
- ``LoadBalancer`` - služba je dostupná z vnější sítě pomocí LoadBalanceru
- ``ExternalName`` - služba je dostupná z vnější sítě pomocí DNS jména

``Headless Service`` - služba, která nemá přiřazenou IP adresu - je využívána přímo IP adresa podu.

Ingress
-------
Objekt, který zajišťuje přístup k aplikacím z vnější sítě. Ingress je možné nastavit pomocí URL, která je přesměrována na danou službu. 

Standardně Ingress podporuje TCP a HTTP/HTTPS. Ve školním clusteru lze použít pouze HTTP s TLS terminací na úrovni Ingressu.

Pokročilé koncepty
==================

NetworkPolicy
-------------

Objekt, který zajišťuje, že se komunikace mezi pody v rámci namespace bude řídit pravidly - například to, že pody z namespace A nemohou komunikovat s pody v namespace B, ale mohou komunikovat s pody v namespace C. Užitečné pro zajištění bezpečnosti.

PodSecurityAdmission
--------------------

Objekt, který zajišťuje, že se pody budou spouštět s určitými bezpečnostními pravidly - například to, že se nepovolí spuštění podu s privilegovaným režimem. Užitečné pro zajištění bezpečnosti. 

Tři základní typy:

- ``Baseline`` - základní pravidla pro spuštění podu. Užívá se ve školním cluster. Například vynuceno ``Privileged`` na ``false``.

- ``Restricted`` - přísnější pravidla pro spuštění podu. Například vynuceno ``RunAsNonRoot``.

- ``Privileged`` - nejvolnější pravidla pro spuštění podu. Bez omezení.

RBAC
----

Role Based Access Control = objekt, který zajišťuje, že se uživatelé budou moci přihlásit do clusteru a budou mít přístup k určitým zdrojům. Užitečné pro zajištění bezpečnosti.

Dělení na dvě části:

- ``Role`` - definuje pravidla pro přístup k zdrojům v rámci namespace.

- ``ClusterRole`` - definuje pravidla pro přístup k zdrojům v rámci celého clusteru.

``RoleBinding`` - definuje, který uživatel nebo skupina uživatelů má přístup k dané roli.

``ClusterRoleBinding`` - definuje, který uživatel nebo skupina uživatelů má přístup k danému cluster roli.

ServiceAccount
--------------

Objekt, který umožňuje, aby pod měl omezený přístup k API serveru.
