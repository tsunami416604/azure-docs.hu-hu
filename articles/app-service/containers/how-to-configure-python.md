---
title: Python-alkalmazások konfigurálása az Azure App Service szolgáltatáshoz Linux rendszeren
description: Ez az oktatóanyag a Python-alkalmazások szerzői műveleteit és konfigurálási lehetőségeit ismerteti az Azure App Service-hez Linux rendszeren.
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
ms.date: 10/09/2018
ms.author: astay;cephalin;kraigb
ms.custom: mvc
ms.openlocfilehash: 9474b2d64c97b6e6d0fc06c3c448fa6e0515e70c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633648"
---
# <a name="configure-your-python-app-for-the-azure-app-service-on-linux"></a>Python-alkalmazás konfigurálása az Azure App Service szolgáltatáshoz Linux rendszeren

Ez a cikk azt ismerteti, hogy a [Linuxon futó Azure App Service](app-service-linux-intro.md) hogyan futtat Python-alkalmazásokat, és szükség esetén hogyan szabhatja testre az App Service viselkedését.

## <a name="set-python-version"></a>Python-verzió beállítása

Két alaplemezképek érhetők el: Python 3.6-os és a Python 3.7-es verzióját. A Python-alapú lemezképhez hozhat létre egy alkalmazást. Ha például létrehoz egy alkalmazást a Python 3.7, futtassa a következő parancsot a a Cloud shellben:

```azurecli-interactive
az webapp create --resource-group <group_name> --plan <plan_name> --name <app_name> --runtime "PYTHON|3.7"
```

Python 3.6-os módosítja a Python-verzió (kép alapján), például futtassa a következő parancsot a Cloud shellben:

```azurecli-interactive
az webapp config set --resource-group <group_name> --name <app_name> --linux-fx-version "PYTHON|3.6"
```

Ha a Python egy másik verziójára van szüksége, ehelyett létre kell hoznia egy saját tárolórendszerképet, és azt kell telepítenie. További információk: [Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban](tutorial-custom-docker-image.md).

## <a name="container-characteristics"></a>A tároló jellemzői

Python-alkalmazások üzembe helyezését futtathatók Docker-tároló a GitHub-adattár definiált Linuxon futó App Service [Python 3.6-os](https://github.com/Azure-App-Service/python/tree/master/3.6.6) vagy [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0).

Ez a tároló a következő jellemzőkkel rendelkezik:

- Az alkalmazások a [Gunicorn WSGI HTTP-kiszolgáló](http://gunicorn.org/) és további argumentumok használatával futnak`--bind=0.0.0.0 --timeout 600`.

- Alapértelmezés szerint az alaprendszerkép magában foglalja a Flask webes keretrendszert, de a tároló a WSGI-vel és a Python 3.7-es verziójával kompatibilis keretrendszereket is támogat, így például a Djangót.

- További csomagok, mint például a Django, telepítéséhez hozzon létre egy [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) fájlt a projekt gyökérkönyvtárában a `pip freeze > requirements.txt` parancs használatával. Ezt követően a Git üzemelő példányával tegye közzé projektjét az App Service-be, amely az alkalmazásfüggőségek telepítéséhez automatikusan futtatja a tárolóban a `pip install -r requirements.txt` parancsot.

## <a name="container-startup-process-and-customizations"></a>Tárolóindítási folyamat és testreszabások

Rendszerindítás során a Linux-tárolóban lévő App Service a következő lépéseket futtatja:

1. Keresse meg és alkalmazza a megfelelő indítási parancsot, ha az meg van adva.
1. Ellenőrizze, hogy létezik-e a Django-alkalmazás *wsgi.py* fájlja, és ha igen, annak használatával indítsa el a Gunicornt.
1. Keresse meg az *application.py* nevű fájlt, és ha megtalálta, indítsa el a Gunicornt a(z) `application:app` használatával, Flask-alkalmazást feltételezve.
1. Ha más alkalmazás nem található, indítson el egy alapértelmezett alkalmazást, amely a tárolóba van beépítve.

A következő szakaszok további információkkal szolgálnak az egyes beállításokról.

### <a name="django-app"></a>Django-alkalmazás

Django-alkalmazások esetén az App Service a(z) `wsgi.py` nevű fájlt keresi az alkalmazáskódban, majd a Gunicorn futtatásához a következő parancsot használja:

```bash
# <module> is the path to the folder containing wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Ha az indítási parancsot szeretné jobban szabályozni, használjon egy [ egyéni indítási parancsot](#custom-startup-command), és cserélje le a(z) `<module>` modult annak a modulnak a nevével, amely tartalmazza a *wsgi.py* fájlt.

### <a name="flask-app"></a>Flask-alkalmazás

A Flask esetében az App Service az *application.py* nevű fájlt keresi, és a Gunicornt a következőképpen indítja el:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 application:app
```

Ha a fő alkalmazásmodul egy másik fájlban található, használjon másik nevet az alkalmazás objektum számára, vagy ha további argumentumok szeretne megadni a Gunicornhoz, használjon egy [egyéni indítási parancsot](#custom-startup-command). Az említett szakasz példájában a Flask a *hello.py* fájlban található belépőkódot, valamint a(z) `myapp` nevű Flask-alkalmazásobjektumot használja.

### <a name="custom-startup-command"></a>Egyéni indítási parancs

A tároló indítási viselkedését egy egyéni Gunicorn indítási parancs megadásával szabályozhatja. Ha például egy olyan Flask-alkalmazással rendelkezik, amelynek a főmodulja a *hello.py*, és a fájlban a Flask-alkalmazásobjektum neve `myapp`, a parancs a következő lesz:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Ha a főmodul egy almappában található (például `website`), a mappát a `--chdir` argumentummal határozhatja meg:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

A Gunicorn számára további argumentumokat is adhat a parancshoz, például: `--workers=4`. További információkért lásd: [A Gunicorn futtatása](http://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Egyéni parancs megadásához tegye a következőket:

1. Keresse meg a [Alkalmazásbeállítások](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) oldalt a Microsoft Azure Portal-on.

1. A **Futásidejű** beállításoknál a **Stack** beállításaként válassza a **Python 3.7** értéket, és adja meg a parancsot közvetlenül az **Indítási fájl** mezőben.

    Azt is megteheti, a parancsot a projekt gyökérkönyvtárában lévő szövegfájlba menti, *startup.txt* (vagy bármilyen más) néven. Ezután ezt a fájlt telepítse az App Service-be, és a fájlnevet adja meg az **Indítási fájl** mezőben. Ez a beállítás lehetővé teszi, hogy a parancsot az Azure Portal helyett a forráskódraktárban kezelje.

1. Kattintson a **Mentés** gombra. Az App Service automatikusan újraindul, és néhány másodperc múlva a rendszer alkalmazza az egyéni indítási parancsot.

> [!Note]
> Az App Service figyelmen kívül hagyja az egyéni parancsfájl feldolgozásakor előforduló hibákat, majd az indítási folyamat következő lépéseként megkeresi a Django- és a Flask-alkalmazásokat. Ha nem várt viselkedést tapasztal, ellenőrizze, hogy az indítási fájl telepítve lett-e az App Service-be, és hogy az nem tartalmaz-e hibákat.

### <a name="default-behavior"></a>Alapértelmezett viselkedés

Ha az App Service nem talál egyéni parancsot, vagy Django-, illetve Flask-alkalmazást, akkor egy alapértelmezett csak olvasható alkalmazást futtat, amely az _opt/defaultsite_ mappában található. Az alapértelmezett alkalmazás a következőképpen jelenik meg:

![Alapértelmezett App Service a Linux webhelyen](media/how-to-configure-python/default-python-app.png)

## <a name="troubleshooting"></a>Hibaelhárítás

- **Saját alkalmazáskódjának telepítése után megjelenik az alapértelmezett alkalmazás.**  Az alapértelmezett alkalmazás azért jelenik meg, mert ténylegesen nem telepítette az alkalmazáskódját az App Service-be, vagy az App Service nem találta meg az alkalmazáskódját, és helyette az alapértelmezett alkalmazást futtatta.
  - Indítsa újra az App Service-t, várjon 15-20 másodpercet, és ellenőrizze újra az alkalmazást.
  - Bizonyosodjon meg róla, hogy az App Service Linux- és nem Windows-alapú példányát használja. Az Azure CLI parancssori felületről futtassa az `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` parancsot, a `<resource_group_name>` és az `<app_service_name>` helyőrzőket megfelelően behelyettesítve. Az `app,linux` kimenetet kell látnia, máskülönben hozza újra létre az App Service szolgáltatást, és válassza a linuxos verziót.
    - SSH- vagy a Kudu konzol használatával közvetlenül csatlakozzon az App Service-hez, és győződjön meg arról, hogy a fájlok léteznek a *site/wwwroot* könyvtárban. Ha a fájlok nem léteznek, tekintse át a telepítési folyamatot, és telepítse újra az alkalmazást.
  - Ha a fájlok léteznek, az App Service nem tudta azonosítani az adott indítási fájlt. Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#custom-startup-command).
  
- **A böngészőben megjelenik „A szolgáltatás nem érhető el” üzenet.** A böngésző az App Service válaszára vára túllépte az időkorlátot, ami azt jelzi, hogy az App Service elindította a Gunicorn-kiszolgálót, de az alkalmazás kódját meghatározó argumentumok helytelenek.
  - Frissítse a böngészőt, különösen akkor, ha az App Service-csomag legalacsonyabb tarifacsomagját használja. Az alkalmazás ingyenes szolgáltatásszintjeink használatakor például indítása hosszabb időt vehet igénybe, de a böngésző frissítése után ismét reagálni fog.
  - Ellenőrizze, hogy az alkalmazás struktúrája megfelel-e annak, amit az App Service a [Django](#django-app) vagy a [Flask](#flask-app) számára elvár, vagy használjon [egyéni indítási parancsot](#custom-startup-command).
  - SSH- vagy Kudu-konzol használatával kapcsolódjon az App Service-hez, majd vizsgálja meg a *LogFiles* mappában tárolt diagnosztikai naplókat. A naplózással kapcsolatos további információkért lásd: [Webalkalmazások diagnosztikai naplózása az Azure App Service szolgáltatásban](../web-sites-enable-diagnostic-log.md).
