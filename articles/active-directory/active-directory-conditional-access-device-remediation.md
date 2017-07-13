---
title: "Innen nem érheti el az Azure Portalon Windows-eszközről| Microsoft Docs"
description: "Ismerje meg, honnan származik az „Innen nem érheti el” üzenet, és mely tényezőknek az ellenőrzésével előzheti meg, hogy belefusson."
services: active-directory
keywords: "eszközalapú feltételes hozzáférés, eszközregisztráció, eszközregisztráció engedélyezése, eszközregisztráció és MDM"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/05/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 4d7704c03bece51957efa2a56577b8808cc62c79
ms.contentlocale: hu-hu
ms.lasthandoff: 07/08/2017


---
# Innen nem érheti el Windows-eszközről
<a id="you-cant-get-there-from-here-on-a-windows-device" class="xliff"></a>

Ha például a szervezet SharePoint Online intranetéhez próbál hozzáférni, megjelenhet egy oldal a következő üzenettel: *Innen nem érheti el*. Az oldal azért jelenik meg, mert a rendszergazda olyan hozzáférési szabályzatot állított be, amely bizonyos feltételek szerint megakadályozza a vállalat erőforrásaihoz való hozzáférést. Bár lehetséges, hogy a probléma megoldásához végül az ügyfélszolgálathoz vagy a rendszergazdához kell fordulni, érdemes először megpróbálkozni néhány lépéssel.

Ha **Windows**-eszközt használ, ellenőrizze a következőket:

- Támogatott böngészőt használ?

- A Windows támogatott verzióját futtatja az eszközön?

- Az eszköz megfelelő?






## Támogatott böngésző
<a id="supported-browser" class="xliff"></a>

Ha a rendszergazda feltételes hozzáférési szabályzatot állított be, akkor csak támogatott böngészővel férhet hozzá a szervezet erőforrásaihoz. Windows-eszközön csak az **Internet Explorer** és az **Edge** támogatott.

Ha egy erőforráshoz egy nem támogatott böngésző miatt nem férhet hozzá, azt könnyen megállapíthatja a hibaoldal részletek szakaszának megtekintésével:

![„Innen nem érheti el” üzenetek nem támogatott böngészők esetén](./media/active-directory-conditional-access-device-remediation/02.png "Forgatókönyv")

Az egyetlen javítási megoldás egy olyan böngésző használata, amelyet az alkalmazás támogat az adott eszközplatformon. A támogatott böngészők teljes listáját a [támogatott böngészők](active-directory-conditional-access-supported-apps.md#supported-browsers-for-device-based-policies) című témakörben találja.  


## A Windows támogatott verziói
<a id="supported-versions-of-windows" class="xliff"></a>

A következő feltételeknek kell teljesülnie az eszközön futó Windows operációs rendszer esetében: 

- Ha eszközén Windows asztali operációs rendszert használ, annak verziója Windows 7 vagy újabb kell, hogy legyen.
- Ha eszközén Windows kiszolgálói operációs rendszert használ, annak verziója Windows Server 2008 R2 vagy újabb kell, hogy legyen. 


## Megfelelő eszköz
<a id="compliant-device" class="xliff"></a>

Lehetséges, hogy a rendszergazda olyan feltételes hozzáférési szabályzatot állított be, amely a vállalat erőforrásaihoz való hozzáférést csak megfelelő eszközökről engedélyezi. A megfeleléshez az eszköznek csatlakoznia kell a helyszíni Active Directoryhez vagy az Azure Active Directoryhez.

Ha egy erőforráshoz egy nem megfelelő eszköz miatt nem férhet hozzá, azt könnyedén megállapíthatja a hibaoldal részletek szakaszának megtekintésével:
 
![„Innen nem érheti el” üzenetek nem regisztrált eszközök esetén](./media/active-directory-conditional-access-device-remediation/01.png "Forgatókönyv")


### Az eszköz egy helyszíni Active Directoryhez csatlakozik?
<a id="is-your-device-joined-to-an-on-premises-active-directory" class="xliff"></a>

**Ha az eszköz egy helyszíni Active Directoryhez csatlakozik a szervezeten belül:**

1. Győződjön meg róla, hogy a munkahelyi fiókot (Active Directory-fiókját) használva lépett be a Windowsba.
2. Csatlakozzon a vállalati hálózathoz virtuális magánhálózaton (VPN) vagy DirectAccessen keresztül.
3. Miután csatlakozott, zárolja a Windows-munkamenetet a Windows-gomb és az L billentyű lenyomásával.
4. Oldja fel a Windows-munkamenet zárolását a munkahelyi fiókjához tartozó hitelesítő adatok beírásával.
5. Várjon egy percet, majd próbálja meg újból elérni az alkalmazást vagy a szolgáltatást.
6. Ha ugyanazt a lapot látja, kattintson a **További részletek** hivatkozásra, és az ott található információt adja át a rendszergazdának.


### Az eszköz nem csatlakozik egy helyszíni Active Directoryhez?
<a id="is-your-device-not-joined-to-an-on-premises-active-directory" class="xliff"></a>

Ha az eszköze nem csatlakozik egy helyszíni Active Directoryhez, és Windows 10 rendszert futtat, két lehetőség áll rendelkezésre:

* Futtassa az Azure AD Joint
* Adja hozzá a munkahelyi vagy iskolai fiókját a Windowshoz

További információt a két megoldás közötti különbségekről itt talál: [Windows 10-es eszközök használata a munkahelyen ](active-directory-azureadjoin-windows10-devices.md).  
Ha az eszköz:

- A szervezethez tartozik, futtassa az Azure AD Joint.
- Egy személyes eszköz vagy Windows-telefon, adja hozzá a munkahelyi vagy iskolai fiókját a Windowshoz 



#### Azure AD Join a Windows 10 rendszeren
<a id="azure-ad-join-on-windows-10" class="xliff"></a>

Az eszköz Azure AD-hez való csatlakoztatásának lépései az eszközön futó Windows 10 verziójához vannak kötve. A Windows 10 operációs rendszer verziójának megállapításához futtassa a **winver** parancsot: 

![Windows-verzió](./media/active-directory-conditional-access-device-remediation/03.png )


**Windows 10 évfordulós frissítés (1607-es verzió):**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Fiókok** > **Hozzáférés munkahelyi vagy iskolai rendszerhez** elemre.
3. Kattintson a **Connect** (Csatlakozás) gombra.
4. Kattintson az **Eszköz csatlakoztatása az Azure AD-hez** elemre.
5. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a bemutatott lépéseket.
6. Jelentkezzen ki, majd jelentkezzen be a munkahelyi fiókjával.
7. Próbálja meg újból elérni az alkalmazást.

**Windows 10, 2015 novemberi frissítés (1511-es verzió):**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Rendszer** > **Névjegy** elemre.
3. Kattintson a **Csatlakozás az Azure AD-hez** elemre.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a bemutatott lépéseket.
5. Jelentkezzen ki, majd jelentkezzen be a munkahelyi fiókjával (Azure AD-fiókjával).
6. Próbálja meg újból elérni az alkalmazást.


#### Munkahelyi csatlakoztatás Windows 8.1 rendszeren
<a id="workplace-join-on-windows-81" class="xliff"></a>

Ha az eszköze nincs tartományhoz csatlakoztatva és Windows 8.1 rendszert futtat, munkahelyi csatlakoztatást hajthat végre, és regisztrálhat a Microsoft Intune-ba a következők elvégzésével:

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Hálózat** > **Munkahely** elemre.
3. Kattintson a **Csatlakozás** parancsra.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a bemutatott lépéseket.
5. Kattintson a **Bekapcsolás** elemre.
6. Próbálja meg újból elérni az alkalmazást.



#### Adja hozzá a munkahelyi vagy iskolai fiókját a Windowshoz
<a id="add-your-work-or-school-account-to-windows" class="xliff"></a> 


**Windows 10 évfordulós frissítés (1607-es verzió):**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Fiókok** > **Hozzáférés munkahelyi vagy iskolai rendszerhez** elemre.
3. Kattintson a **Connect** (Csatlakozás) gombra.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a bemutatott lépéseket.
5. Próbálja meg újból elérni az alkalmazást.


**Windows 10, 2015 novemberi frissítés (1511-es verzió):**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Fiókok** > **Saját fiókok** elemre.
3. Kattintson a **Munkahelyi vagy iskolai fiók beállítása** elemre.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a bemutatott lépéseket.
5. Próbálja meg újból elérni az alkalmazást.





## Következő lépések
<a id="next-steps" class="xliff"></a>
[Azure Active Directory feltételes hozzáférés](active-directory-conditional-access.md)


