---
title: A beépített Python-rendszerkép konfigurálása az Azure App Service-ben
description: Ez az oktatóanyag egy Python-alkalmazás beépített Python-rendszerképpel történő konfigurálási lehetőségeit és szerzői műveleteit ismerteti az Azure App Service-ben.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228552"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>A beépített Python-rendszerkép konfigurálása az Azure App Service-ben (előzetes verzió)

Ez a cikk bemutatja, hogyan konfigurálhatja a beépített Python-rendszerképet az [Linuxon futó App Service-ben](app-service-linux-intro.md) a Python-alkalmazások futtatásához.

## <a name="python-version"></a>Python-verzió

A Python-futtatókörnyezet a Linuxon futó App Service-ben a `python-3.7.0` verziót használja.

## <a name="supported-frameworks"></a>Támogatott keretrendszerek

A `python-3.7` futtatókörnyezettel kompatibilis, Web Server Gateway Interface (WSGI) megfelelőséggel rendelkező webkeretrendszerek minden verziója támogatott.

## <a name="package-management"></a>Csomagkezelés

Git-közzététel során a Kudu motor keresi a [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files) fájlt keresi az adattár gyökérkönyvtárában, és automatikusan telepíti a csomagot az Azure-ban `pip` használatával.

Közzététel előtt a fájl létrehozásához keresse meg az adattár gyökérkönyvtárát, és futtassa a következő parancsot a Python-környezetben:

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>A Python-alkalmazás konfigurálása

Az App Service-ben a beépített Python-rendszerkép a [Gunicorn](http://gunicorn.org/) kiszolgálót használja a Python-alkalmazás futtatására. A Gunicorn egy Python WSGI HTTP-kiszolgáló UNIX rendszerhez. Az App Service automatikusan konfigurálja a Gunicorn kiszolgálót Django- és Flask-projektekhez.

### <a name="django-app"></a>Django-alkalmazás

Ha egy `wsgi.py` modult tartalmazó Django-projektet, az Azure automatikusan meghívja a Gunicorn kiszolgálót a következő paranccsal:

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Flask-alkalmazás

Ha Flask-alkalmazást tesz közzé, és a belépési pont egy `application.py` vagy `app.py` modulban van, az Azure a következő parancsok egyikével automatikusan meghívja a Gunicorn kiszolgálót:

```bash
gunicorn application:app
```

Vagy 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>Indítás testreszabása

Az alkalmazáshoz egyéni belépési pont meghatározásához először hozzon létre egy _.txt_ fájlt egyéni Gunicorn-paranccsal, és helyezze azt el a projekt gyökerében. Ha például a kiszolgálót a _helloworld.py_ modullal és az `app` változóval szeretné elindítani, hozzon létre egy _startup.txt_ fájlt a következő tartalommal:

```bash
gunicorn helloworld:app
```

Az [Alkalmazásbeállítások](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) lapon a **Futtatókörnyezeti verem** értékeként válassza a **Python | 3.7** lehetőséget, és adja meg az előző lépésben meghatározott **Indító fájl** nevét. Például: _startup.txt_.
