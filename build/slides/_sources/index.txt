
.. AMQP slides file, created by
   hieroglyph-quickstart on Sat Sep 17 12:48:18 2016.


============================
 Rapide présentaion de AMQP
============================
Illustré par rabbitmq

.. toctree::
   :maxdepth: 2

   amqp

Pourquoi
========

 * propager un message, une information, un évenement ou un ordre à travers un SI
 * illustration avec EDD

Approche SGBD SQL
=================

 * champ avec un état

 * Remplacer le cron qui fait :

.. code-block:: sql

 UPDATE message WHERE new = 't' RETURNING content ;


ou pire :

.. code-block:: sql

 UPDATE message WHERE new = 't' RETURNING content LIMIT 1;

Problèmes:a
 * manque de perf,
 * manque de scalabilité
 * quid de 10, 20, 50 slaves ?
 * quid de différents types de message, type de subscriber ?

Solution ?
============

 Une file de message !

 * redis
 * rq
 * mqtt
 * xmpp
 * amqp


AMQP
====

 * définition
 * url
 * standard OASIS


Exchange et Queue
=================

Exchange
========

Queue
=====

Routing Key
===========

Type d'exchange
===============

 * direct
 * fanout
 * topic
 * header

Direct
======

Fanout
======

Topic
=====
 * joker `*` et `#`

Header
======

RabbitMQ
========

 * url
 * licence
 * limites
