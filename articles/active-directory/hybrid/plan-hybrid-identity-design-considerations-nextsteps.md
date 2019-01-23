---
title: Az Azure Active Directory hibrid identitás tervezési szempontok – következő lépések |} A Microsoft Docs
description: Egy szinopszist és a hibrid Identitáskezelés – kialakítási szempontok útmutató elolvasása után követő lépések
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 02d48768-ea9e-4bfe-ae54-b54c4bd0a789
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 37c89771823649f6a2590911ba41eaf64c6ccbd8
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474421"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations--next-steps"></a>Az Azure Active Directory hibrid identitás tervezési szempontok – következő lépések
Most, hogy végrehajtotta a követelmények meghatározását és megvizsgálta az összes lehetséges a mobileszköz-felügyeleti megoldás, készen áll a következő lépésekkel jobbra, és a szervezet a támogató infrastruktúra üzembe helyezéséhez.

## <a name="hybrid-identity-solutions"></a>Hibrid identitáskezelési megoldások
-Az igényeihez igazodó konkrét megoldási forgatókönyvek felhasználásával kiválóan alkalmas a áttekintheti és megtervezheti egy mobileszköz-kezelési infrastruktúra üzembe helyezésének részleteit. A következő megoldások felvázolják a leggyakrabban használt mobileszköz-felügyeleti forgatókönyvek:

* A [mobileszközök és számítógépek kezelése vállalati környezetekben megoldás](https://technet.microsoft.com/library/dn582037.aspx) segítséget nyújt a mobileszközök kezelése a helyszíni System Center 2012 Configuration Manager infrastruktúrát kiterjeszti a felhőben a Microsoft Intune-nal. A hibrid infrastruktúra segítségével informatikai szakemberek a közepes és nagy méretű környezetekben lehetővé BYOD és a távelérés közben csökkenthetik az Adminisztráció összetettségét.
* A [mobileszköz-felügyelet a Configuration Manager 2007 megoldás](https://technet.microsoft.com/library/dn508400.aspx) segítséget nyújt a mobileszközök felügyeletét, amikor az infrastruktúra alapja a System Center Configuration Manager 2007. A megoldás bemutatja, hogyan állítható be egy kiszolgálót a System Center 2012 Configuration Manager fut, majd futtassa a Microsoft Intune és a mobileszköz-felügyeleti képességeit használja ki.
* A [kezelése kis környezetekben megoldás a mobileszközök](https://technet.microsoft.com/library/dn715906.aspx) szól kisvállalkozások támogatását szeretnék megvalósítani. Ismerteti, hogyan bővítheti a jelenlegi infrastruktúrát a mobileszköz-kezelés és a BYOD támogatásához a Microsoft Intune használatával. Ez a megoldás a Microsoft Intune használatával egy önálló, csak felhőalapú konfigurációban helyi kiszolgálókkal nem rendelkező támogatott legegyszerűbb forgatókönyvet ismerteti.

## <a name="hybrid-identity-documentation"></a>Hibrid identitáskezelési dokumentáció
Elméleti és eljárástervezési, telepítési és felügyeleti tartalmak hasznosak lehetnek a mobileszköz-kezelési megoldás megvalósításához:

* [A Microsoft System Center](https://technet.microsoft.com/library/cc507089.aspx) megoldások segíthetnek rögzítési és összesítheti az infrastruktúrával, házirendek, folyamatok és ajánlott eljárások kapcsolatos tudnivalókat, hogy az informatikai munkatársak kezelhető rendszereket építhetnek és automatizálhatják a műveleteket.
* [A Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx) van egy felhőalapú Eszközkezelési szolgáltatás, amely segít a számítógépek és mobileszközök kezeléséhez, és a vállalati adatok védelméhez.
* [Office 365 MDM](https://technet.microsoft.com/library/ms.o365.cc.devicepolicy.aspx) lehetővé teszi, hogy a kezelése és a mobil eszközök, az Office 365-szervezethez csatlakozó. Office 365 MDM segítségével eszközbiztonsági házirendeket és hozzáférési szabályokat állíthat be, és a mobileszközök törlése, ha az elvesztett vagy ellopott használhatja.

## <a name="hybrid-identity-resources"></a>Hibrid identitás erőforrások
A következő erőforrások figyelés gyakran kapcsolatos legfrissebb hírekhez, és frissíti a mobileszköz-felügyeleti megoldások:

* [A Microsoft nagyvállalati mobilitási blog](https://cloudblogs.microsoft.com/ENTERPRISEMOBILITY/)
* [A Microsoft a The Cloud blog](https://blogs.technet.com/b/in_the_cloud/)
* [A Microsoft Intune blogján](https://blogs.technet.com/b/microsoftintune/)
* [A Microsoft System Center Configuration Manager-blog](https://blogs.technet.com/b/configurationmgr/)
* [A Microsoft System Center Configuration Manager munkacsoportjának blogján](https://blogs.technet.com/b/configmgrteam/)

## <a name="see-also"></a>Lásd még
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

