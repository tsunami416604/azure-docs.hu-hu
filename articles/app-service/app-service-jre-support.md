---
title: A Windows futásidő-támogatás Java az Azure App Service
description: Ez a témakör a Java-futtatókörnyezet utasítást az Azure App Service-támogatás a Windows.
author: bmitchell287
ms.author: brendm; joshuapa
ms.date: 3/22/2019
ms.topic: article
ms.service: app-service
manager: dougE
ms.openlocfilehash: ab0e9b9f272108745b629d2cb284f6c5ab52c76f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522957"
---
# <a name="java-runtime-statement-of-support-for-app-service-on-windows"></a>Java runtime utasítás a Windows App Service-támogatása

## <a name="jdk-versions-and-maintenance"></a>JDK-verziók és karbantartás

Az Azure támogatott Java fejlesztői készlet (JDK) van [Zulu](https://www.azul.com/downloads/azure-only/zulu/) keresztül [Azul Systems](https://www.azul.com/).

Főverzió-frissítései a Windows Azure App Service-ben új futásidejű beállítások keresztül biztosítunk. Ügyfelek frissítése a Java ezen újabb verziói az alkalmazásszolgáltatás üzemelő példányának konfigurálásával, és felelős tesztelése, és biztosítja a fő frissítés megfelel az igényeiknek.

Támogatott segítségével negyedévente automatikusan javítani a januárban, áprilisban, júliusban és minden év október a.

## <a name="security-updates"></a>Biztonsági frissítések

Javítások és javításokat tartalmaz olyan súlyos biztonsági réseket elérhető lesz, amint az Azul Systems elérhetővé válnak. "Nagyobb" biztonsági rés alapján pontot 9.0-s vagy újabb a [NIST közös biztonsági rés pontozási rendszert, 2. verzió](https://nvd.nist.gov/cvss.cfm).

## <a name="deprecation-and-retirement"></a>És a használatból való kivonást egyaránt

Ha egy támogatott Java-futtatókörnyezet megszűnik, az érintett modul használatával Azure-fejlesztők számára kap elavulással kapcsolatos figyelmeztetés legalább hat hónapos előtt a futtatókörnyezet kivonják a forgalomból.

## <a name="local-development"></a>Helyi fejlesztés

A fejlesztők is letölthetik a éles kiadása az Azul Zulu Enterprise JDK a helyi fejlesztési [Azul a letöltési hely](https://www.azul.com/downloads/azure-only/zulu/).

## <a name="development-support"></a>Fejlesztés támogatása

Terméktámogatási a [Azure által támogatott Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) Microsoft érhető el, az Azure fejlesztésének vagy [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) az egy [minősített Azure-támogatási csomag](https://azure.microsoft.com/support/plans/).

## <a name="runtime-support"></a>Podpora modulu Runtime

A fejlesztők is [nyissa meg a probléma](/azure/azure-supportability/how-to-create-azure-support-request) az Azure-támogatási, ha rendelkezik a Azul Zulu segítségével egy [minősített támogatási csomag](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>További lépések

Ez a témakör a Java-futtatókörnyezet utasítást az Azure App Service-támogatás a Windows.
- További információ az Azure App Service tekintse meg a webalkalmazás üzemeltetéséhez [App Service-ben – áttekintés](overview.md).
- További információ a Java az Azure-fejlesztési: [Azure Java fejlesztői központ a](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
