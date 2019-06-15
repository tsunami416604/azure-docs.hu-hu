---
title: Konfigurálja a Windows a Java-alkalmazások – az Azure App Service |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az alapértelmezett Windows-példányok az Azure App Service-ben futó Java alkalmazások.
keywords: az Azure app Service-ben, a web app, a windows, a nyílt forráskódú, a java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: 82e8936a888cbc99088ab18423e55dd57a3c2e77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65604141"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurálja a Windows Azure App Service-Java-alkalmazás

Azure App Service használatával gyorsan elkészítheti, telepítheti és méretezheti a Tomcat a Java-fejlesztőknek vagy a Java Standard Edition (SE) alkalmazáscsomag-webalkalmazásokat az olyan teljes körűen felügyelt Windows-szolgáltatás. A parancssorból vagy a-szerkesztők, mint például az intellij-vel, az Eclipse vagy a Visual Studio Code Maven beépülő modulok az alkalmazások központi telepítése.

Ez az útmutató a főbb fogalmakat és használata App Service-ben Java-fejlesztőknek készült utasításokat tartalmaz. Ha korábban nem használta az Azure App Service, olvassa végig a [Java rövid](app-service-web-get-started-java.md) első. Általános kérdések az App Service használatával kapcsolatban, amelyek nem a Java fejlesztési adott válaszok a [App Service Windows – gyakori kérdések](faq-configuration-and-management.md).

> [!NOTE]
> Nem találja, amit keres? Tekintse át a [Windows nyílt Forráskódú – gyakori kérdések](faq-configuration-and-management.md) vagy a [Linuxos Java-beállítási útmutató](containers/configure-language-java.md) üzembe helyezése és a Java-alkalmazás biztonságossá tétele kapcsolatos információkat.

## <a name="configuring-tomcat"></a>Tomcat konfigurálása

Tomcat szerkesztése `server.xml` vagy más konfigurációs fájlokat, először jegyezze fel az a Tomcat főverzió a portálon.

1. Keresse meg a Tomcat kezdőkönyvtár-verzióra vonatkozó futtatásával a `env` parancsot. Keresse meg a környezeti változó, amely a következővel kezdődik: `AZURE_TOMCAT`és a főverzió megfelel-e. Ha például `AZURE_TOMCAT85_HOME` a Tomcat 8.5 a Tomcat könyvtárra mutat.
1. Miután azonosította a Tomcat kezdőkönyvtár-verzióra vonatkozó, másolja a konfigurációs könyvtár `D:\home`. Például ha `AZURE_TOMCAT85_HOME` érték volt `D:\Program Files (x86)\apache-tomcat-8.5.37`, a másolt konfigurációs könyvtár teljes elérési útja lehet `D:\home\tomcat\conf`.

Végezetül indítsa újra az App Service. Az üzemelő példányokat kell Ugrás `D:\home\site\wwwroot\webapps` ugyanúgy, mint korábban.

## <a name="java-runtime-statement-of-support"></a>Java runtime rendszerállapot-támogatás

### <a name="jdk-versions-and-maintenance"></a>JDK-verziók és karbantartás

Az Azure támogatott Java fejlesztői készlet (JDK) van [Zulu](https://www.azul.com/downloads/azure-only/zulu/) keresztül [Azul Systems](https://www.azul.com/).

Főverzió-frissítései a Windows Azure App Service-ben új futásidejű beállítások keresztül biztosítunk. Ügyfelek frissítése a Java ezen újabb verziói az alkalmazásszolgáltatás üzemelő példányának konfigurálásával, és felelős tesztelése, és biztosítja a fő frissítés megfelel az igényeiknek.

Támogatott segítségével negyedévente automatikusan javítani a januárban, áprilisban, júliusban és minden év október a.

### <a name="security-updates"></a>Biztonsági frissítések

Javítások és javításokat tartalmaz olyan súlyos biztonsági réseket elérhető lesz, amint az Azul Systems elérhetővé válnak. "Nagyobb" biztonsági rés alapján pontot 9.0-s vagy újabb a [NIST közös biztonsági rés pontozási rendszert, 2. verzió](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>És a használatból való kivonást egyaránt

Ha egy támogatott Java-futtatókörnyezet megszűnik, az érintett modul használatával Azure-fejlesztők számára kap elavulással kapcsolatos figyelmeztetés legalább hat hónapos előtt a futtatókörnyezet kivonják a forgalomból.

### <a name="local-development"></a>Helyi fejlesztés

A fejlesztők is letölthetik a éles kiadása az Azul Zulu Enterprise JDK a helyi fejlesztési [Azul a letöltési hely](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Fejlesztés támogatása

Terméktámogatási a [Azure által támogatott Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) Microsoft érhető el, az Azure fejlesztésének vagy [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) az egy [minősített Azure-támogatási csomag](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Podpora modulu Runtime

A fejlesztők is [nyissa meg a probléma](/azure/azure-supportability/how-to-create-azure-support-request) az Azure-támogatási, ha rendelkezik a Azul Zulu segítségével egy [minősített támogatási csomag](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>További lépések

Ez a témakör a Java-futtatókörnyezet utasítást az Azure App Service-támogatás a Windows.

- További információ az Azure App Service tekintse meg a webalkalmazás üzemeltetéséhez [App Service-ben – áttekintés](overview.md).
- További információ a Java az Azure-fejlesztési: [Azure Java fejlesztői központ a](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
