---
title: Telepítse át a klasszikus szabályzatokkal az Azure portálon |} Microsoft Docs
description: Ismerje meg, hogy szüksége lehet áttelepíteni a klasszikus szabályzatokkal az Azure portálon.
services: active-directory
keywords: alkalmazások, a feltételes hozzáférés az Azure ad-vel, a biztonságos hozzáférés a vállalati erőforrásokhoz, a feltételes hozzáférési házirendekkel a feltételes hozzáférés
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: b6285381833526cdbdd7c1392894458a47c1cf34
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723728"
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Telepítse át a klasszikus szabályzatokkal az Azure portálon 


[Feltételes hozzáférés](active-directory-conditional-access-azure-portal.md) egy Azure Active Directory (Azure AD), amelyek segítségével szabályozhatja, hogy hogyan funkció engedélyezett a felhasználók hozzáférést a felhőalapú alkalmazásokat. Erre a célra továbbra is megegyezik, amíg az új Azure-portálon kiadása a feltételes hozzáférés működését jelentős fejlesztéseket vezetett be.

Érdemes lehet a házirendek nem hozott létre az Azure portálon mert áttelepítése:

- Ön nem lehet kezelni előtt forgatókönyvek most meg lehet oldani.

- Csökkentése érdekében egyesítse ezeket által felügyelni kell házirendek száma.   

- Kezelheti egy központi helyen a feltételes hozzáférési szabályzatokat.

- A klasszikus Azure portálon rendszerből.   

Ez a cikk ismerteti a meglévő feltételes hozzáférési házirendek áttelepítéséhez az új keretrendszer tudnia kell.
 
## <a name="classic-policies"></a>Klasszikus szabályzatok

Az a [Azure-portálon](https://portal.azure.com), a [feltételes hozzáférés - házirendek](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lap, a belépési pontot a feltételes hozzáférési szabályzatok. Azonban a környezetben, előfordulhat, hogy akkor is feltételes hozzáférési házirendek nem hozott létre a lapon. Ezek a házirendek nevezik *klasszikus házirendek*. Klasszikus házirendek feltételes hozzáférési házirendeket, hogy létrehozta a:

- a klasszikus Azure portálon
- Az Intune klasszikus portál
- Az Intune App Protection portálon


Az a **feltételes hozzáférés** lap, a klasszikus házirendek eléréséhez kattintson [ **klasszikus házirendek (előzetes verzió)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) a a **kezelése** a szakasz. 


![Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


A **klasszikus házirendek** nézet lehetővé teszi egy lehetőség, hogy:

- A klasszikus házirendek szűréséhez.
 
    ![Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- Tiltsa le a klasszikus házirendek.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- Tekintse át a beállításokat, a klasszikus házirendek (és le kell tiltani).

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


Klasszikus házirend le van tiltva, ha többé nem tudja visszaállítani ezt a lépést. Ezért a csoport tagságát egy klasszikus házirend használatával módosíthatja a **részletek** megtekintése. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

Módosításával vagy a kiválasztott csoportoknak vagy kizárja meghatározott csoportok néhány tesztfelhasználók letiltott klasszikus házirend hatásának tesztelheti a házirend az összes belefoglalt felhasználók és csoportok letiltása előtt. 



## <a name="azure-ad-conditional-access-policies"></a>Az Azure AD feltételes hozzáférési házirendek

A feltételes hozzáférés az Azure portálon egyetlen központi helyen a házirendek segítségével kezelheti. A feltételes hozzáférés megvalósítása jelentősen megváltozott, mert tanulmányozza át az alapfogalmakat a klasszikus házirendek áttelepítése előtt.

Lásd:

- [Feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md) főbb fogalmait és kifejezéseit és terminológiát.

- [Ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md) üzembe helyezni a feltételes hozzáférés a szervezet bizonyos útmutatást eléréséhez.

- [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md) és ismerje meg az Azure-portálon a felhasználói felületen.


 
## <a name="migration-considerations"></a>Az áttelepítés szempontjai

Ebben a cikkben az Azure AD feltételes hozzáférési házirendeket is nevezzük *új házirendek*.
A klasszikus házirendek továbbra is működnek az új házirendek és tiltsa le vagy törölje őket. 

A következő szempontokat fontosak házirend összesítés környezetében:

- Klasszikus házirendek egy meghatározott felhőalkalmazás vannak társítva, míg az új házirendet kell annyi felhőalapú alkalmazásokat is választhat.

- Klasszikus házirend és a cloud app új házirend vezérlőelemek megkövetelése minden vezérlő (*és*) teljesítendő. 


- Egy új házirend a következőket teheti:
 
    - Több feltétel össze, ha az adott esetben szükséges. 

    - Válassza ki a több biztosítani vonatkoznak, mint a hozzáférés szabályozása és kombinálása egy logikai *vagy* (van szükség a kijelölt vezérlők egyik) vagy egy logikai *és* (a kijelölt vezérlőelemeket szükséges).

        ![Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Az Office 365 Exchange online-hoz

Ha az áttelepítendő klasszikus házirendeket **Office 365 Exchange online-hoz** , amelyek tartalmaznak **az Exchange Active Sync** ügyfél alkalmazások feltételként, előfordulhat, hogy nem fogja tudni konszolidálhatják ezeket egy új házirendet. 

Ez helyzet, például a Ha azt szeretné, hogy minden ügyfél alkalmazástípusok támogatásához. Egy új házirendet, amely rendelkezik a **az Exchange Active Sync** ügyfél alkalmazások feltételként nem választhat más ügyfél alkalmazásokat.

![Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

A konszolidáció be egy új házirend nem lehetséges, ha a klasszikus házirendek a különböző feltételeket tartalmaz. Egy új házirendet, amely rendelkezik **az Exchange Active Sync** ügyfélalkalmazások, konfigurált feltétel nem támogatja a más feltételek:   

![Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

Ha egy új házirendet, amely rendelkezik **az Exchange Active Sync** ügyfélalkalmazások, konfigurált feltétel, meg kell győződnie, hogy más feltételek nem. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

[Alkalmazás-alapú](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) klasszikus szabályzatok az Office 365 Exchange Online, amely tartalmazza az **az Exchange Active Sync** ügyfél alkalmazások feltételként engedélyezése **támogatott** és **nemtámogatott** [eszközplatformok](active-directory-conditional-access-technical-reference.md#device-platform-condition). Az egyes eszközplatformok kapcsolódó új házirendet nem lehet konfigurálni, amíg a támogatási korlátozhatja [támogatott eszközplatformok](active-directory-conditional-access-technical-reference.md#device-platform-condition) csak. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

A konszolidáció több klasszikus házirendeket, amelyek tartalmaznak **az Exchange Active Sync** ügyfél alkalmazások feltételként, ha telepítve:

- Csak **az Exchange Active Sync** feltételként 

- Néhány követelményt a konfigurált hozzáférés

Egy általános forgatókönyv a összevonása:

- Eszközalapú klasszikus házirend a klasszikus Azure portálon 
- Az alkalmazás-alapú klasszikus házirend az Intune app védelmi portál 
 
Ebben az esetben a klasszikus házirendek is konszolidálhatják egy új házirendet, amely mindkét kiválasztott követelményekkel rendelkezik.

![Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>Eszközplatformok

Klasszikus szabályzatok [alkalmazás-alapú vezérlők](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) előre konfigurált, az iOS és Android, amelyek a [eszköz platform feltétel](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

Az új kell kiválasztania a [eszközplatformok](active-directory-conditional-access-technical-reference.md#device-platform-condition) szeretne támogatni külön-külön.

![Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>További lépések

- Ha meg szeretné ismerni a feltételes hozzáférési házirend konfigurálása tudnivalókat [Ismerkedés a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal-get-started.md).

- Ha készen áll a környezet feltételes hozzáférési házirend-beállításokkal, tekintse meg a [ajánlott eljárások a feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-best-practices.md). 
