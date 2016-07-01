<properties
   pageTitle="Alkalmazás- vagy felhasználóspecifikus Marathon-szolgáltatás | Microsoft Azure"
   description="Alkalmazás- vagy felhasználóspecifikus Marathon-szolgáltatás létrehozása"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Containers, Marathon, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# Alkalmazás- vagy felhasználóspecifikus Marathon-szolgáltatás létrehozása

Az Azure tárolószolgáltatás előkonfigurált Apache Mesos és Marathon rendszerű főkiszolgálókat biztosít. Ezek használhatók a fürtön található alkalmazásainak vezénylésére is, de jobb, ha a főkiszolgálókat nem erre a célra használja. A Marathon konfigurációjának finomhangolásához például a magukra a főkiszolgálókra történő bejelentkezésre, illetve módosításokra van szükség;  így könnyen létrejöhetnek a szabványostól eltérő, egyedi főkiszolgálók, amelyeket emiatt függetlenül kell kezelni és felügyelni. Ezenkívül nem biztos, hogy az egyik csoport számára megfelelő konfiguráció egy másik csoport számára is optimális lesz. Ebben a cikkben elmagyarázzuk, hogyan vehet fel felhasználó- vagy alkalmazásspecifikus Marathon-szolgáltatásokat.

Mivel ez a szolgáltatás egyetlen felhasználóhoz vagy csoporthoz fog tartozni, tetszés szerint, szabadon konfigurálható. Az Azure tárolószolgáltatás továbbá a szolgáltatás folyamatos működését is biztosítja; amennyiben a szolgáltatás nem működik, az Azure tárolószolgáltatás újraindítja. Az esetek többségében nem is lehet észrevenni a kiesést.

## Előfeltételek

[Az Azure tárolószolgáltatás egy DCOS vezénylőtípusú példányának üzembe helyezése](container-service-deployment.md), [az ügyfél és a fürt közötti kapcsolat feltételeinek megteremtése](container-service-connect.md), valamint [AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)].

## Alkalmazás- vagy felhasználóspecifikus Marathon-szolgáltatás létrehozása.

Először hozzon létre egy JSON-konfigurációs fájlt, amely meghatározza a létrehozni kívánt alkalmazásszolgáltatás nevét. A jelen példában használjuk a `marathon-alice` megnevezést a keretrendszer neveként. Mentse a fájlt például a `marathon-alice.json` néven:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Ezután a DC/OS parancssori felülete segítségével telepítse a Marathon-példányt a konfigurációs fájlban megadott beállításokkal:

```bash
dcos package install --options=marathon-alice.json marathon
```

Ekkor a DC/OS felhasználói felületének Services (Szolgáltatások) lapján már látnia kell, hogy fut a `marathon-alice` szolgáltatás. Ha közvetlenül szeretne csatlakozni, a felhasználói felületet a `http://<hostname>/service/marathon-alice/` címen találja.

## A DC/OS parancssori felület beállítása a szolgáltatás elérésére

A DC/OS parancssori felület igény szerint konfigurálható ezen új szolgáltatás elérésére a `marathon.url` tulajdonságnak `marathon-alice` példányra mutató beállításával, a következőképpen:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

A `dcos config show` paranccsal ellenőrizheti, hogy a Marathon melyik példányával működik a parancssori felület. A fő Marathon-szolgáltatásra a `dcos config unset marathon.url` paranccsal állíthatja vissza.


<!--HONumber=Jun16_HO2-->


