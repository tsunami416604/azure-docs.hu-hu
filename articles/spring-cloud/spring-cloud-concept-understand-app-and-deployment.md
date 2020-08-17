---
title: Az alkalmazások és az üzembe helyezés ismertetése az Azure Spring Cloud-ban
description: Az alkalmazás és az üzembe helyezés közötti különbségtétel az Azure Spring Cloud-ban.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 81e1925810f374da6f02bf6c3a013b00b5bb9a2c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263972"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Az alkalmazások és az üzembe helyezés ismertetése az Azure Spring Cloud-ban

Az Azure Spring Cloud erőforrás-modelljében az **alkalmazás** és a **telepítés** a két kulcsfontosságú fogalom. Az Azure Spring Cloud-ban az *alkalmazások* egy üzleti alkalmazás vagy egy szolgáltatás absztrakciója.  Egy *központi telepítésben*az *alkalmazás* futtatásakor telepített kód vagy bináris fájl egy verziója.

 ![Alkalmazások és központi telepítések](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

Az Azure Spring Cloud standard csomag lehetővé teszi, hogy egy alkalmazás egy éles üzembe helyezést és egy átmeneti üzembe helyezést biztosítson, így a kék/zöld üzembe helyezés egyszerűen elvégezhető.

## <a name="app"></a>Alkalmazás
Az alkalmazás szintjén az alábbi funkciók/tulajdonságok vannak meghatározva.

| Enumeráció | Definíció |
|:--|:----------------|
| Nyilvános</br>Végpont | Az alkalmazás eléréséhez használt URL-cím |
| Egyéni</br>Tartomány | Az egyéni tartomány védelmét biztosító CNAME-rekord |
| Szolgáltatás</br>Kötés | A fájl és a *ServiceBusTrigger* attribútum function.jsban beállított kötési konfigurációs tulajdonságok |
| Felügyelt</br>Identitás | A felügyelt identitás Azure Active Directory lehetővé teszi, hogy az alkalmazás könnyedén hozzáférhessen más Azure AD-védelemmel ellátott erőforrásokhoz, például a Azure Key Vault |
| Állandó</br>Tárolás | Beállítás, amely lehetővé teszi, hogy az alkalmazások az alkalmazás újraindítása után is megmaradjanak |

## <a name="deployment"></a>Üzembe helyezés

A következő funkciók/tulajdonságok a telepítési szinten vannak meghatározva, és az üzemi/átmeneti telepítés cseréjekor lesznek kicserélve.

| Enumeráció | Definíció |
|:--|:----------------|
| CPU | Virtuális mag-példányok száma |
| Memória | Beállítás, amely memóriát foglal le a vertikális felskálázáshoz vagy az üzembe helyezéshez |
| Példány</br>Darabszám | Az alkalmazás példányainak száma, manuális vagy automatikus beállítás |
| Automatikus méretezés | A példányok számának automatikus méretezése előre definiált szabályok és ütemtervek alapján |
| JVM</br>Beállítások | beállítás: JAVA_OPTS |
| Környezet</br>Változók | A teljes Azure Spring Cloud-környezetre érvényes beállítások |
| Futtatókörnyezet</br>Verzió | Java 8/Java 11|

## <a name="restrictions"></a>Korlátozások

* Az **alkalmazásnak rendelkeznie kell egy éles üzembe helyezéssel**: az üzemi környezet törlését az API blokkolja. A törlés előtt ki kell cserélni az átmeneti állapotba.
* **Egy alkalmazás legfeljebb két üzemelő példányban lehet**: a kettőnél több üzemelő példány létrehozása le van tiltva az API-ban. Telepítse az új bináris fájlt a meglévő éles környezetbe vagy átmeneti üzembe helyezésre.
* **Az üzembe helyezési felügyelet az alapszintű szinten nem érhető el**: standard szintű csomag használata a kék-zöld üzembe helyezési lehetőséghez.

## <a name="see-also"></a>Lásd még
* [Átmeneti környezet beállítása az Azure Spring Cloud-ban](spring-cloud-howto-staging-environment.md)