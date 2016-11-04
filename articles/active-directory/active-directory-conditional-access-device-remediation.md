---
title: 'Hibaelhárítás: Innen nem érheti el | Microsoft Docs'
description: Ez a témakör segít egy alkalmazás hozzáférésnek biztosításához szükséges javítási lépések beazonosításában.
services: active-directory
keywords: eszközalapú feltételes hozzáférés, eszközregisztráció, eszközregisztráció engedélyezése, eszközregisztráció és MDM
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: markvi

---
# Hibaelhárítás: Innen nem érheti el
Hozzáférés megtagadva lapot kap, ha olyan alkalmazáshoz fért hozzá, mint például a SharePoint Online.  
Mi a következő lépés?

Ez az útmutató segít egy alkalmazás hozzáférésnek biztosításához szükséges és elérhető javítási lépések beazonosításában.

Milyen eszközplatform fut az eszközén?
Az erre a kérdésre adott válasz határozza meg a szükséges témakörszakaszt:

* Windows-eszköz
* iOS-eszköz (iPhone vagy iPad)
* Android-eszköz

## Elérés Windows-eszközről
Ha az eszközén Windows 10, Windows 8.1, Windows 8.0, Windows 7, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszer fut, válassza a megfelelő okot annak a lapnak az azonosításával, amelyet akkor kapott, amikor megpróbálta elérni az alkalmazást.

### Az eszköz nincs regisztrálva
Ha az eszköze nincs regisztrálva az Azure Active Directoryban (Azure AD) és az alkalmazást eszközalapú házirend védi, az alábbi tartalommal rendelkező lapot láthatja:

![„Innen nem érheti el” üzenetek nem regisztrált eszközök esetén](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

Ha az eszköze tartomány részeként van csatlakoztatva az Active Directoryhoz a szervezetében, megpróbálhatja a következőt:

1. Győződjön meg róla, hogy a munkahelyi fiókot (Active Directory-fiók) használva lépett be a Windowsba.
2. Csatlakozzon a vállalati hálózathoz VPN-en vagy DirectAccessen keresztül.
3. Miután csatlakozott, zárolja a Windows-munkamenetet a Windows-gomb és az L billentyű lenyomásával.
4. Oldja fel a Windows-munkamenet zárolását a munkahelyi fiókjához tartozó hitelesítő adatok beírásával.
5. Várjon egy percet, majd próbálja meg újból elérni az alkalmazást.
6. Ha ugyanazt a lapot látja, lépjen kapcsolatba a rendszergazdával, kattintson a **További részletek** hivatkozásra, majd adja meg a részleteket.

Ha az eszköze nincs tartományhoz csatlakoztatva és Windows 10 rendszert futtat, két lehetőség áll rendelkezésre:

* Futtassa az Azure AD Joint.
* Adja hozzá a munkahelyi vagy iskolai fiókját a Windowshoz.

További információt a két megoldás közötti különbségekről itt talál: [Using Windows 10 devices in your workplace](active-directory-azureadjoin-windows10-devices.md) (Windows 10-es eszközök használata a munkahelyen).

Az Azure AD Join futtatásához tegye a következőket (Windows Phone-telefonon nem érhető el):

**Windows 10 évfordulós frissítés**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Fiókok** > **Hozzáférés munkahelyi vagy iskolai rendszerhez** elemre.
3. Kattintson a **Connect** (Csatlakozás) gombra.
4. Kattintson a lap alján lévő **Eszköz csatlakoztatása az Azure AD-hez** elemre.
5. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a lépéseket a befejezésig.
6. Jelentkezzen ki, majd jelentkezzen be a munkahelyi fiókjával.
7. Próbálja meg újból elérni az alkalmazást.

**Windows 10, 2015 novemberi frissítés**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Rendszer** > **Névjegy** elemre.
3. Kattintson a **Csatlakozás az Azure AD-hez** elemre.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a lépéseket a befejezésig.
5. Jelentkezzen ki, majd jelentkezzen be a munkahelyi fiókjával (Azure AD-fiók).
6. Próbálja meg újból elérni az alkalmazást.

Munkahelyi vagy iskolai fiók hozzáadásához tegye a következőket:

**Windows 10 évfordulós frissítés**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Fiókok** > **Hozzáférés munkahelyi vagy iskolai rendszerhez** elemre.
3. Kattintson a **Connect** (Csatlakozás) gombra.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a lépéseket a befejezésig.
5. Próbálja meg újból elérni az alkalmazást.

**Windows 10, 2015 novemberi frissítés**

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Fiókok** > **Saját fiókok** elemre.
3. Kattintson a **Munkahelyi vagy iskolai fiók beállítása** elemre.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a lépéseket a befejezésig.
5. Próbálja meg újból elérni az alkalmazást.

Ha az eszköze nincs tartományhoz csatlakoztatva és Windows 8.1 rendszert futtat, munkahelyi csatlakoztatást hajthat végre, és regisztrálhat a Microsoft Intune-ba, a következők elvégzésével:

1. Nyissa meg a **Gépházat**.
2. Kattintson a **Hálózat** > **Munkahely** elemre.
3. Kattintson a **Csatlakozás** parancsra.
4. Hitelesítse magát szervezeténél, biztosítson többtényezős hitelesítési adatokat, ha szükséges, majd kövesse a lépéseket a befejezésig.
5. Kattintson a **Bekapcsolás** elemre.
6. Várjon a befejezésig.
7. Próbálja meg újból elérni az alkalmazást.

## Nem támogatott böngészők
Ha az alkalmazást a következő böngészőkön keresztül éri el, a korábban látott laphoz hasonló lap jelenik meg:

* Chrome, Firefox, vagy bármely egyéb olyan böngésző, amely nem a Microsoft Edge vagy a Microsoft Internet Explorer Windows 10 vagy Windows Server 2016 rendszer esetén.
* Firefox Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszer esetén.

![„Innen nem érheti el” üzenetek nem támogatott böngészők esetén](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")

Az egyetlen javítási megoldás egy olyan böngésző használata, amelyet az alkalmazás támogat az adott eszközplatformon.

## Elérés iOS-eszközről
Térjen vissza később az iPhone-ra és iPadre vonatkozó utasításokért.

## Elérés Android-eszközről
Térjen vissza később az androidos telefonokra és táblagépekre vonatkozó utasításokért.

## Következő lépések
[Azure Active Directory feltételes hozzáférés](active-directory-conditional-access.md)

<!--HONumber=Sep16_HO4-->


