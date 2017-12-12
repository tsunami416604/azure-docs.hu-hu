---
title: "Az Azure Active Directory hibrid identitáskezelési tervezési szempontok - lépések |} Microsoft Docs"
description: "Egy összegzést és mindenképpen olvassa el a hibrid Identitáskezelés – kialakítási szempontokat útmutató követő lépések"
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 02d48768-ea9e-4bfe-ae54-b54c4bd0a789
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 29c045af81134847321d9ef69943f0154e9ee5a0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-hybrid-identity-design-considerations--next-steps"></a>Az Azure Active Directory hibrid identitáskezelési kialakítási szempontok-következő lépések
Most, hogy befejezte a követelmények meghatározását és megvizsgálta az összes lehetséges mobileszköz-kezelési megoldást, készen áll a következő lépésekre jogot az Ön és szervezete támogató infrastruktúra üzembe helyezéséhez.

## <a name="hybrid-identity-solutions"></a>Hibrid identitáskezelési megoldások
-Segítségével áttekintheti és megtervezheti egy mobileszköz-kezelési infrastruktúra üzembe részleteinek kihasználva konkrét megoldási forgatókönyvek az igényeihez. A következő megoldások felvázolják a leggyakrabban használt mobileszköz-kezelési forgatókönyveket:

* A [mobileszközök és számítógépek kezelése vállalati környezetekben megoldás](https://technet.microsoft.com/library/dn582037.aspx) mobileszközök kezelése a helyszíni System Center 2012 Configuration Manager infrastruktúrát kiterjeszti a felhőbe a Microsoft Intune segítségével. Ez a hibrid infrastruktúrával informatikai szakemberek a közepes és nagy méretű környezetekben lehetővé BYOD és a távelérés közben csökkenthetik az Adminisztráció összetettségét.
* A [Configuration Manager 2007-megoldás mobileszköz-kezelés](https://technet.microsoft.com/library/dn508400.aspx) segítséget nyújt a mobileszközök felügyeletéhez, amikor az infrastruktúra alapja a System Center Configuration Manager 2007. A megoldás bemutatja, hogyan telepíthet úgy egyetlen kiszolgáló System Center 2012 Configuration Manager fut, akkor utána a Microsoft Intune futtatásával kihasználhassa annak mobileszköz-kezelési képességeit.
* A [mobileszközök felügyeletéhez a kisebb környezetekben megoldás](https://technet.microsoft.com/library/dn715906.aspx) szól kis számára készült, amelyeknek támogatását szeretnék megvalósítani. Ismerteti, hogyan bővítheti a jelenlegi infrastruktúrát a mobileszköz-kezelés és a BYOD támogatásához a Microsoft Intune-nal. Ez a megoldás a legegyszerűbb, a Microsoft Intune-t önálló, csak felhőalapú konfigurációban, helyi kiszolgáló nélkül használja támogatott forgatókönyvet írja le.

## <a name="hybrid-identity-documentation"></a>Hibrid identitáskezelési dokumentáció
Az elméleti és eljárástervezési, telepítési és felügyeleti tartalmak hasznosak a mobileszköz-kezelési megoldás megvalósításához:

* [A Microsoft System Center](https://technet.microsoft.com/library/cc507089.aspx) megoldások segítségével rögzítheti és összesítheti, az infrastruktúra, házirendek, folyamatok és ajánlott eljárások kapcsolatos tudnivalókat, hogy az informatikai munkatársak kezelhető rendszereket építhetnek és automatizálhatják a műveleteket.
* [A Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx) egy felhőalapú Eszközkezelési szolgáltatás, amely segít a számítógépek és mobileszközök kezeléséhez, és a vállalati adatok védelméhez.
* [Az Office 365 MDM](https://technet.microsoft.com/library/ms.o365.cc.devicepolicy.aspx) lehetővé teszi kezelése mobileszközöket és biztosítsa azok az Office 365-szervezethez csatlakozó. Használhatja az Office 365 MDM segítségével eszközbiztonsági házirendeket és hozzáférési szabályokat állíthat be, és ha az elvesztett vagy ellopott mobileszközökről.

## <a name="hybrid-identity-resources"></a>Hibrid identitás erőforrások
A következő források követésével gyakran a legújabb híreket biztosít, és frissíti a mobileszköz-kezelési megoldások:

* [A Microsoft nagyvállalati mobilitási blog](http://blogs.technet.com/b/enterprisemobility/)
* [A Felhőhöz a Microsoft blog](http://blogs.technet.com/b/in_the_cloud/)
* [A Microsoft Intune blogja](http://blogs.technet.com/b/microsoftintune/)
* [Microsoft System Center Configuration Manager blog](http://blogs.technet.com/b/configurationmgr/)
* [Microsoft System Center Configuration Manager munkacsoportjának blogja](http://blogs.technet.com/b/configmgrteam/)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)

