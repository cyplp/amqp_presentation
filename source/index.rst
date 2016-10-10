
.. AMQP slides file, created by
   hieroglyph-quickstart on Sat Sep 17 12:48:18 2016.


============================
 Rapide présentaion de AMQP
============================


Pourquoi
========

 * propager un message, une information, un évenement ou un ordre à travers un SI

.. figure:: _static/ftp_worker.png
   :scale: 50 %

   illustration avec EDD


Approche SGBD SQL
=================

Solution champ avec un état

.. code-block:: sql

  CREATE TABLE message(id_message SERIAL,
                       content TEXT,
		       new BOOLEAN default 't');

.. figure:: _static/ftp_db_worker.png
   :scale: 40 %


Code
====
pull avec :

.. code-block:: sql

 UPDATE message SET new = 'f' WHERE new = 't' RETURNING content ;


ou pire :

.. code-block:: sql

 UPDATE message SET new = 'f' WHERE new = 't' RETURNING content LIMIT 1;

Problèmes
=========

 * manque de performance,
 * manque de scalabilité,
 * quid de 10, 20, 50 slaves ?
 * quid de différents types de message, type de subscriber ?

Solution ?
==========

Une file de message !

.. figure:: _static/ftp_queue_worker.png
   :scale: 50 %

Quelle file de message ?
========================

* redis
 * rq
 * mqtt
 * xmpp
 * amqp


AMQP
====

 * définition

   **Advanced Message Queuing Protocol**

 * urls

   - http://www.amqp.org/
   - https://fr.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol

 * standard OASIS

Protocole
=========

 * broker
 * but
 * protocole binaire
 * TLS intégré
 * `amqp://user:password@host/vhost`

Quelques Dates
==============

 - premier draft : 2003
 - version 1.0 : 2011

Exchange et queue
=================

 - on ne peut que écrire dans un exchange,
 - on ne peut que lire dans une queue,
 - entre les 2 le binding.

Exchange et queue
=================

.. figure:: _static/exchange_et_queue.png
   :scale: 50 %


Message
=======

 - livré une fois et une seule fois,
 - routing key,
 - header,
 - payload,
 - acquitement.

Type d'exchange
===============

 * direct
 * fanout
 * topic
 * header

Direct
======

Le message est envoyé sur chacune des queues en fonction de la routing key.

Fanout
======

Le message est envoyé sur chacune des queues en quelque soit de la routing key.

Topic
=====
 * joker `*` et `#`,
 * Le message est envoyé selon le match à la RT.

Header
======


Autres notions
==============

 * connection
 * channel
 * priority
 * exchange, queue, message temporaire
 * ttl

RabbitMQ
========

 * url
 * licence
 * limites et pieges
 * autre aspects
 * cluster

personnal e-branding
====================

 * twitter : @cyplp
 * github
