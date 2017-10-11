---
title: "Klasszikus portál Azure AD alkalmazás Proxy összekötők |} Microsoft Docs"
description: "Bemutatja, hogyan adhat az Azure AD alkalmazásproxy összekötők csoportok létrehozásához és kezeléséhez."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: b283796a-9679-4c79-b703-802bb850f65d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: fc65c4053c45d9c16c62ee0fe65924133a4bb94a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Külön hálózatok és helyek összekötő csoportokat használnak az alkalmazások közzététele
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-application-proxy-connectors-azure-portal.md)
> * [klasszikus Azure portál](active-directory-application-proxy-connectors.md)
>
>

Összekötő csoportok hasznosak több, különböző esetekre, beleértve:

* A több összekapcsolt adatközpontokkal helyek. Ebben az esetben meg szeretné tartani az adatközponton belül mértékű forgalom lehető mert kereszt-datacenter hivatkozások drága, és a lassú. Csak az adatközponton belül a alkalmazásokhoz kiszolgálására mindkét adatközpont összekötők telepítése. Ezt a módszert minimálisra csökkenti a kereszt-datacenter hivatkozásokat, és teljes mértékben transzparens élményt nyújt a felhasználók számára.
* Elszigetelt hálózatokban, amelyek nem a fő vállalati hálózat részei a telepített alkalmazások kezelése. Összekötő csoportok segítségével dedikált összekötők telepítése is különítheti el a hálózati alkalmazások elkülönített hálózatokon.
* Összekötő csoportok felhőalapú férhetnek hozzá az infrastruktúra-szolgáltatási a telepített alkalmazások, adja meg egy közös szolgáltatás a hozzáférés az alkalmazásokhoz. Összekötő csoportok nem további függőség létrehozása a vállalati hálózaton, vagy a felhasználói élményt darabolható. Összekötők minden felhőbeli adatközpontot is telepíthető, és csak ezen a hálózaton lévő alkalmazások kiszolgálására. Magas rendelkezésre állás biztosítása érdekében több összekötő is telepítheti.
* Többerdős környezetben, amelyben adott összekötők erdőnként telepítése és beállítása az adott alkalmazások kiszolgálására támogatása.
* Összekötő csoportok használhatók vész-helyreállítási helyeken vagy a feladatátvételi észlelni, vagy biztonsági másolat az elsődleges hely.
* Összekötő csoportok több vállalatot kiszolgálására egyetlen bérlőtől is használható.

## <a name="prerequisite-create-your-connectors"></a>Előfeltétel: Az összekötő létrehozása
Az összekötők csoportosításához [több összekötők telepítése](active-directory-application-proxy-enable.md), majd a nevet, és csoportosításához. Végül kell rendelnie őket adott alkalmazásokra.

## <a name="step-1-create-connector-groups"></a>1. lépés: Az összekötő csoportok létrehozása
Összekötő csoportot hozhat létre. Összekötő-csoport létrehozása a klasszikus Azure portálon hajtható végre.

1. Válassza ki azt a címtárat, és kattintson a **konfigurálása**.  
    ![Alkalmazásproxy, konfigurálja a képernyőfelvétel - kattintson összekötő csoportok kezelése](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)
2. Az alkalmazásproxy, kattintson **összekötő csoportok kezelése** , és hozzon létre egy összekötő csoport azzal, hogy a csoport nevét.  
    ![Application proxy connector csoportok képernyőfelvétel - új csoport neve](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>2. lépés: A csoportok összekötők hozzárendelése
Az összekötő csoportok jönnek létre, ha az összekötők áthelyezése a megfelelő csoporthoz.

1. A **alkalmazásproxy**, kattintson a **összekötők kezelése**.
2. A **csoport**, válassza ki a csoportot, minden-összekötőhöz. Az összekötők válik az új csoport aktív legfeljebb 10 percig is eltarthat.  
    ![Application proxy összekötők képernyőfelvétel - legördülő menüből válassza csoport](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>3. lépés: Az összekötő-csoportokat alkalmazások hozzárendelése
Az utolsó lépést is, hogy az összekötő csoporthoz, azt látja, hogy minden alkalmazáshoz.

1. A klasszikus Azure portálon, a könyvtárban, válassza ki az alkalmazást, rendelje hozzá a csoporthoz, és kattintson a kívánt **konfigurálása**.
2. A **összekötő csoport**, válassza ki a használni kívánt alkalmazást a csoportot. Ez a változás azonnal lesz alkalmazva.  
    ![Application proxy connector csoport képernyőfelvétel - legördülő menüből válassza csoport](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)

## <a name="see-also"></a>Lásd még:
* [Alkalmazásproxy engedélyezése](active-directory-application-proxy-enable.md)
* [Egyszeri bejelentkezés engedélyezése](active-directory-application-proxy-sso-using-kcd.md)
* [Feltételes hozzáférés engedélyezése](active-directory-application-proxy-conditional-access.md)
* [Az alkalmazásproxy problémák elhárítása](active-directory-application-proxy-troubleshoot.md)

A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](http://blogs.technet.com/b/applicationproxyblog/).
