---
title: Biztonsági edzés az AKS virtuálisgép-üzemeltetiállomásokon
description: További információ az AKS virtuálisgép-gazdagép operációs rendszerének biztonsági megerősítéséről
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594380"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>Biztonsági edzés az AKS virtuálisgép-üzemeltetiállomásokon 

Az Azure Kubernetes-szolgáltatás (AKS) egy soc, ISO, PCI DSS és HIPAA szabványoknak megfelelő biztonságos szolgáltatás. Ez a cikk az AKS virtuálisgép-üzemeltetésre alkalmazott biztonsági megerősítést ismerteti. Az AKS biztonságáról az [Azure Kubernetes-szolgáltatás (AKS) alkalmazások és fürtök biztonsági fogalmai](https://docs.microsoft.com/azure/aks/concepts-security)című témakörben talál további információt.

Az AKS-fürtök gazdavirtuális gépeken vannak telepítve, amelyek egy biztonságoptimalizált operációs rendszert futtatnak. Ez a gazdaoperációs rendszer jelenleg egy Ubuntu 16.04.LTS-lemezképen alapul, amely további biztonsági megerősítési lépéseket alkalmaz (lásd: Biztonsági megerősítés részletei).   

A biztonság megerősített gazdaoperációs rendszer célja, hogy csökkentse a támadás felületét, és biztonságos módon engedélyezze a tárolók telepítését. 

> [!Important]
> A biztonsági edzett operációs rendszer nem cis benchmarked. Bár átfedések vannak a CIS-referenciaértékekkel, a cél nem az, hogy a CIS-nek megfelelőek legyenek. A gazdaoperációs rendszer megerősítésének célja, hogy a Microsoft saját belső gazdagépbiztonsági szabványainak megfelelő biztonsági szinten közelítsen. 

## <a name="security-hardening-features"></a>Biztonsági edzési funkciók 

* Az AKS alapértelmezés szerint egy biztonságra optimalizált gazdaoperációs rendszert biztosít. Jelenleg nincs lehetőség alternatív operációs rendszer kiválasztására. 

* Az Azure napi javításokat (beleértve a biztonsági javításokat is) alkalmaz az AKS virtuálisgép-üzemelteti. Néhány ilyen javítás újraindítást igényel, míg mások nem. Ön felelős az AKS virtuálisgép-gazdagép újraindításának szükség szerint ütemezésért. Az AKS-javítások automatizálásával kapcsolatos útmutatásért tekintse meg az [AKS-csomópontok javítását.](https://docs.microsoft.com/azure/aks/node-updates-kured)

Az alábbiakban összefoglaljuk a képedzési munkát, amely et az AKS-Engine a biztonságra optimalizált gazdaoperációs rendszer előállításához valósítja meg. A munka [ebben a GitHub-projektben](https://github.com/Azure/aks-engine/projects/7)valósult meg.  

Az AKS-Engine jelenleg nem támogatja és nem tartja be az adott biztonsági előírásokat, de a CIS (Center for Internet Security) naplózási azonosítók adott esetben kényelmi célokat szolgálnak. 

## <a name="whats-configured"></a>Mi van konfigurálva?

| Cis  | Könyvvizsgálati leírás| 
|---|---|
| 1.1.1.1 |A cramfs fájlrendszerek felszerelésének letiltása|
| 1.1.1.2 |A freevxfs fájlrendszerek felszerelésének letiltása|
| 1.1.1.3 |A jffs2 fájlrendszerek csatlakoztatásának letiltása|
| 1.1.1.4 |A HFS fájlrendszerek csatlakoztatásának letiltása|
| 1.1.1.5 |A HFS Plus fájlrendszerek csatlakoztatásának letiltása|
|1.4.3 |Az egyfelhasználós módban szükséges hitelesítés biztosítása |
|1.7.1.2 |A helyi bejelentkezési figyelmeztető szalagcím megfelelő konfigurálásának ellenőrzése |
|1.7.1.3 |A távoli bejelentkezésre figyelmeztető szalagcím megfelelő konfigurálásának biztosítása |
|1.7.1.5 |Az /etc/issue engedélyei konfigurálásának biztosítása |
|1.7.1.6 |Az /etc/issue.net engedélyeinek konfigurálása |
|2.1.5 |Győződjön meg arról, hogy a --streaming-connection-indle-timeout nincs 0-ra állítva |
|3.1.2 |A csomagátirányítás küldésének letiltása |
|3.2.1 |Győződjön meg arról, hogy a forrás útválasztású csomagok nem fogadhatók el |
|3.2.2 |Az ICMP átirányítások nem elfogadása |
|3.2.3 |Győződjön meg arról, hogy a biztonságos ICMP-átirányításokat nem fogadják el |
|3.2.4 |A gyanús csomagok naplózásának ellenőrzése |
|3.3.1 |Az IPv6-útválasztó-hirdetmények nem elfogadottak |
|3.5.1 |A DCCP letiltásának biztosítása |
|3.5.2 |Az SCTP letiltásának biztosítása |
|3.5.3 |Az RDS letiltásának biztosítása |
|3.5.4 |Tipc letiltásának biztosítása |
|4.2.1.2 |A naplózás konfigurálásának ellenőrzése |
|5.1.2 |Az /etc/crontab engedélyek konfigurálásának biztosítása |
|5.2.4 |Az SSH X11 továbbítás letiltásának biztosítása |
|5.2.5 |Annak biztosítása, SSH MaxAuthTries van beállítva, hogy 4 vagy kevesebb |
|5.2.8 |Az SSH gyökérbejelentkezés letiltásának biztosítása |
|5.2.10 |Az SSH PermitUserEnvironment letiltásának biztosítása |
|5.2.11 |Győződjön meg arról, hogy csak jóváhagyott MAX algoritmusokat használ |
|5.2.12 |Az SSH idle időkorlátjának konfigurálása |
|5.2.13 |Győződjön meg róla, hogy az SSH LoginGraceTime beállítása legapródódik is egy percre |
|5.2.15 |Az SSH figyelmeztető szalagcím konfigurálásának biztosítása |
|5.3.1 |A jelszó-létrehozási követelmények konfigurálásának ellenőrzése |
|5.4.1.1 |A jelszó lejáratának 90 napos vagy annál rövidebb ideje |
|5.4.1.4 |Az inaktív jelszózárolás 30 napos vagy annál rövidebb |
|5.4.4 |Annak biztosítása, hogy az alapértelmezett felhasználó umask 027 vagy szigorúbb legyen |
|5.6 |A su parancshoz való hozzáférés korlátozásának biztosítása|

## <a name="additional-notes"></a>További megjegyzések
 
* A támadási felület további csökkentése érdekében néhány szükségtelen kernelmodul-illesztőprogram le van tiltva az operációs rendszerben. 

* A biztonsági megerősített operációs rendszer NEM támogatott az AKS platformon kívül. 

## <a name="next-steps"></a>További lépések  

Az AKS biztonságáról az alábbi cikkekben talál további információt: 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Az AKS biztonsági szempontjai](https://docs.microsoft.com/azure/aks/concepts-security)

[Az AKS ajánlott eljárásai](https://docs.microsoft.com/azure/aks/best-practices)
