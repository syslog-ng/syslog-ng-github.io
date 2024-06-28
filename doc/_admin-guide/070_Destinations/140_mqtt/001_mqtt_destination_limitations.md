---
title: Limitations to using the mqtt() destination
id: adm-dest-mqtt-lim
description: >-
    In this chapter you can read about the limitations of the mqtt() destination in {{ site.product.short_name }}.
---

Using the mqtt() destination of {{ site.product.short_name }} has the following
limitations:

- You can only use the mqtt() destination with {{ site.product.short_name }} version
    3.33 or higher.

- You cannot use the mqtt() destination without installing the the
    eclipse-paho-mqtt-c library.

    For more information about how you can download and install the
    eclipse-paho-mqtt-c library, see Eclipse Paho on the Eclipse Foundation website.

- The current implementation of the mqtt() destination supports
    versions 3.1 and 3.1.1 of the MQTT protocol.
