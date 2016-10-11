
.. AMQP slides file, created by
   hieroglyph-quickstart on Sat Sep 17 12:48:18 2016.


============================
 Rapide présentation de AMQP
============================


Pourquoi
========

 * propager un message, une information, un évènement ou un ordre à travers un SI

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

 UPDATE message SET new = 'f'
        WHERE new = 't'
	RETURNING content;


ou pire :

.. code-block:: sql

 UPDATE message SET new = 'f'
         WHERE new = 't'
	 RETURNING content
	 LIMIT 1;

Problèmes
=========

 * manque de performance,
 * manque de scalabilité,
 * quid de 10, 20, 50 slaves ?
 * quid de différents types de message, type de subscriber ?


Pourquoi ? bis
==============

 * Pour faire de l'asynchrone (Fire & Forget),
 * pour faire du rpc.

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

 * à l'origine pour les salles de marchés
 * broker
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

Type d'exchange
===============

 * direct
 * fanout
 * topic
 * header

Direct
======

.. figure:: _static/direct.png
   :scale: 50 %

Le message est envoyé sur chacune des queues en fonction de la routing key.
 * un message avec la routing key **foo** est reçu par **queue1** et **queue2**,
 * un message avec la routing key **bar** est reçu par **queue2** et **queue3**,
 * un message avec la routing key **baz** n'est pas routé.

Fanout
======

.. figure:: _static/direct.png
   :scale: 50 %

Le message est envoyé sur chacune des queues en quelque soit de la routing key.

Topic
=====

.. figure:: _static/topic.png
   :scale: 50 %

Le message est envoyé selon le match à la routing key.

 * un message avec la routing key **foo.bar** est reçu par **queue1** et **queue2**,
 * un message avec la routing key **foo.bar.baz** est reçu par **queue2** et **queue3**,
 * un message avec la routing key **baz.bar** est reçu par **queue2**.


Topic
=====

.. figure:: _static/topic.png
   :scale: 50 %

Le message est envoyé selon le match à la routing key.

 * un message avec la routing key **baz.bar.foo** est reçu par **queue3**,
 * un message avec la routing key **bar.baz.foo** n'est pas routé.

Header
======

Similaire au direct avec un match exact sur les headers.


Cas possible 1
==============
 Un exchange, des queues

.. figure:: _static/topic.png
   :scale: 50 %

Cas possible 2
==============
 des exchanges, une queue

.. figure:: _static/exchanges_queue.png
   :scale: 50 %

Cas possible 3
==============
 des exchanges, des queues

.. figure:: _static/exchanges_queues.png
   :scale: 50 %

Cas possible 4
==============
 des exchanges, des exchanges, des queues

.. figure:: _static/exchanges_exchanges_queues.png
   :scale: 50 %

Message
=======

 - dans une queue, un message livré une fois et une seule fois,
 - header,
 - payload,
 - acquitement.


Autres notions
==============

 * prefetch
 * priority
 * exchange, queue, message temporaire
 * ttl
 * connection
 * channel
 * heartbeat

Inconvenients et limites
========================

 * fire & forget,
 * on ne sait pas ce qu'il y a dans les files,
 * comment tracer l'emission et la reception d'un message ?
 * le client doit être au courant du cluster,
 * asynchrone oui mais attention.


Exemple producteur
==================

.. code-block:: python
 :linenos:

 from kombu import Connection, Exchange, Queue

 media_exchange = Exchange('media', 'direct', durable=True)
 video_queue = Queue('video', exchange=media_exchange,
		     routing_key='video')

 # connections
 with Connection('amqp://guest:guest@localhost//') as conn:
     # produce
     producer = conn.Producer(serializer='json')
     producer.publish({'name': '/tmp/lolcat1.avi', 'size': 1301013},
                       exchange=media_exchange, routing_key='video',
                       declare=[video_queue])

Exemple Consomateur
===================

.. code-block:: python
 :linenos:

 def process_media(body, message):
     print body
     message.ack()


 # Consume from several queues on the same channel:
 video_queue = Queue('video', exchange=media_exchange, key='video')
 image_queue = Queue('image', exchange=media_exchange, key='image')

 with connection.Consumer([video_queue, image_queue],
                          callbacks=[process_media]) as consumer:
     while True:
         connection.drain_events()

RabbitMQ
========

 * url : https://www.rabbitmq.com
 * licence : Mozilla Public Licence
 * limites et pieges
 * autre aspects
 * cluster
 * https://www.rabbitmq.com/getstarted.html


Personnal e-branding
====================

 * twitter : @cyplp
 * irc : cyp sur #paulla sur freenode
 * github : https://github.com/cyplp/amqp_presentation
