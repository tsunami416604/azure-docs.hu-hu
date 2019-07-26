---
title: Windows Java-alkalmazások konfigurálása – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat Java-alkalmazásokat a Azure App Service alapértelmezett Windows-példányain való futtatáshoz.
keywords: Azure app Service, webalkalmazás, Windows, OSS, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: c1ea306d8a6b5c1876ac6a9288820e1592dbfda6
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498516"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Windows Java-alkalmazás konfigurálása Azure App Servicehoz

Azure App Service lehetővé teszi a Java-fejlesztők számára, hogy a Tomcat vagy Java Standard Edition (SE) csomagban csomagolt webalkalmazásokat a teljes körűen felügyelt, Windows-alapú szolgáltatáson keresztül gyorsan építsék, telepítsék és méretezheti. Alkalmazásokat telepíthet a Maven beépülő modulokból a parancssorból vagy olyan szerkesztőkből, mint például a IntelliJ, az Eclipse vagy a Visual Studio code.

Ez az útmutató a Java-fejlesztőknek a App Service-ban való használatával kapcsolatos főbb fogalmakat és útmutatást tartalmazza. Ha még soha nem használta a Azure App Servicet, először olvassa el a [Java](app-service-web-get-started-java.md) rövid útmutatóját. A Java-fejlesztésre nem jellemző App Service használatára vonatkozó általános kérdéseket a [Windows app Service gyakori](faq-configuration-and-management.md)kérdések című szakaszban találja.

> [!NOTE]
> Nem találja, amit keres? A Java-alkalmazás üzembe helyezésével és védelmével kapcsolatos információkért tekintse meg a [Windows OSS GYIK](faq-configuration-and-management.md) vagy a [Java Linux konfigurációs útmutatót](containers/configure-language-java.md) .

## <a name="configuring-tomcat"></a>A Tomcat konfigurálása

A Tomcat `server.xml` vagy más konfigurációs fájlok szerkesztéséhez először jegyezze fel a Tomcat főverzióját a portálon.

1. A `env` parancs futtatásával keresse meg a verzióhoz tartozó tomcat-kezdőkönyvtárat. Keresse meg azt a környezeti változót, `AZURE_TOMCAT`amely a-val kezdődik, és megfelel a főverziónak. Például a Tomcat `AZURE_TOMCAT85_HOME` 8,5-es tomcat-könyvtárába mutat.
1. Miután azonosította a-verzióhoz tartozó tomcat-kezdőkönyvtárat, másolja a konfigurációs könyvtárat `D:\home`a következőre:. Ha `AZURE_TOMCAT85_HOME` például a `D:\Program Files (x86)\apache-tomcat-8.5.37`értéke volt, a másolt könyvtár új elérési útja a következő lesz `D:\home\apache-tomcat-8.5.37`:.

Végezetül indítsa újra a App Service. A központi telepítéseknek ugyanúgy kell `D:\home\site\wwwroot\webapps` megjelenniük, mint korábban.

## <a name="java-runtime-statement-of-support"></a>A Java futtatókörnyezet támogatási nyilatkozata

### <a name="jdk-versions-and-maintenance"></a>JDK-verziók és-karbantartás

Az Azure által támogatott Java Development Kit (JDK) a [Azul Systems](https://www.azul.com/)által biztosított [Zulu](https://www.azul.com/downloads/azure-only/zulu/) .

A főverzió frissítései a Windows Azure App Service új futtatókörnyezeti lehetőségein keresztül lesznek elérhetők. Az ügyfelek a Java újabb verzióit frissítik a App Service üzembe helyezésének konfigurálásával, és felelősek a fő frissítés megfelelőségének teszteléséhez és biztosításához.

A támogatott JDK minden év januárjában, áprilisban, júliusban és októberben automatikusan megtörténik.

### <a name="security-updates"></a>Biztonsági frissítések

A főbb biztonsági rések javításait és javításait a rendszer azonnal felszabadítja, amint azok elérhetők lesznek a Azul Systems-től. A "fő" biztonsági rést az 9,0-es vagy újabb alappontszám határozza meg a [NIST Common sebezhetőségi pontozási rendszer 2](https://nvd.nist.gov/cvss.cfm). verziójában.

### <a name="deprecation-and-retirement"></a>Elavulás és nyugdíjazás

Ha egy támogatott Java-futtatókörnyezet megszűnik, az érintett futtatókörnyezetet használó Azure-fejlesztők elavult értesítést kapnak a futtatókörnyezet kivonása előtt legalább hat hónappal.

### <a name="local-development"></a>Helyi fejlesztés

A fejlesztők letölthetik az Azul Zulu Enterprise JDK éles kiadását helyi fejlesztésre az [Azul letöltési](https://www.azul.com/downloads/azure-only/zulu/)webhelyéről.

### <a name="development-support"></a>Fejlesztési támogatás

Az Azure [által támogatott Azul ZULU JDK-](https://www.azul.com/downloads/azure-only/zulu/) vel kapcsolatos terméktámogatás a Microsofton keresztül érhető el az Azure-hoz való fejlesztéshez, illetve a [Azure stack](https://azure.microsoft.com/overview/azure-stack/) egy [minősített Azure-támogatási csomaggal](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Podpora modulu Runtime

A fejlesztők az Azure-támogatással megnyithatják az Azul Zulu JDK [kapcsolatos problémákat](/azure/azure-supportability/how-to-create-azure-support-request) , ha rendelkeznek [minősített támogatási csomaggal](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>További lépések

Ez a témakör a Java Runtime Azure App Service Windows rendszeren történő támogatására vonatkozó utasításait ismerteti.

- Ha többet szeretne megtudni a webalkalmazások Azure App Serviceről való üzemeltetéséről, tekintse meg a [app Service áttekintését](overview.md).
- További információ az Azure-beli Java-fejlesztésről: [Azure for Java fejlesztői központ](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
