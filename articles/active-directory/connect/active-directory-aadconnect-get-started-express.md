---
title: "Azure AD Connect: Első lépések a gyorsbeállításokkal | Microsoft Docs"
description: "Ismerje meg, hogyan töltheti le, telepítheti és futtathatja az Azure AD Connect telepítővarázslóját."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: d3b634bc36e01f8586b6645515942c049701c30e
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Első lépések az Azure AD Connecttel a gyorsbeállítások használatával
Az Azure AD Connect **Express Settings** (Gyorsbeállítások) akkor használható, ha egyerdős topológiával rendelkezik, és a hitelesítéshez [jelszó-szinkronizálást](active-directory-aadconnectsync-implement-password-synchronization.md) alkalmaz. Az **Express Settings** (Gyorsbeállítások) az alapértelmezett beállítás, és ez használatos a leggyakoribb üzembe helyezési forgatókönyvhöz. Csak pár rövid kattintásnyira van attól, hogy kiterjessze helyszíni címtárát a felhőre.

Mielőtt elkezdené telepíteni az Azure AD Connect szolgáltatást, bizonyosodjon meg róla, hogy [letöltötte az Azure AD Connectet](http://go.microsoft.com/fwlink/?LinkId=615771), és elvégezte az [Azure AD Connect: Hardware and prerequisites](active-directory-aadconnect-prerequisites.md) (Azure AD Connect: hardver és előfeltételek) témakörben leírt lépéseket.

Ha a gyorsbeállítások nem felelnek meg a topológiának, lásd az egyéb forgatókönyvek [vonatkozó dokumentációját](#related-documentation).

## <a name="express-installation-of-azure-ad-connect"></a>Az Azure AD Connect gyorstelepítése
Ezeket a lépéseket működés közben a [Videók](#videos) szakaszban tekintheti meg.

1. Jelentkezzen be helyi rendszergazdaként a kiszolgálóra, amelyen az Azure AD Connectet telepíteni kívánja. Ezt azon a kiszolgálón érdemes megtennie, amelyiket szinkronizálási kiszolgálónak szánja.
2. Keresse meg az **AzureADConnect.msi** fájlt, és kattintson rá duplán.
3. Az üdvözlőképernyőn jelölje be a licencfeltételek elfogadását jelző mezőt, és kattintson a **Continue** (Folytatás) gombra.  
4. Az Express settings (Gyorsbeállítások) képernyőn kattintson a **Use express settings** (Gyorsbeállítások használata) lehetőségre.  
   ![Üdvözli az Azure AD Connect](./media/active-directory-aadconnect-get-started-express/express.png)
5. A Connect to Azure AD (Csatlakozás az Azure AD szolgáltatáshoz) képernyőn adja meg az Azure AD egy globális rendszergazdai fiókjának felhasználónevét és jelszavát. Kattintson a **Tovább** gombra.  
   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/active-directory-aadconnect-get-started-express/connectaad.png) Ha hibaüzenetet, kap és problémák adódnak a kapcsolódással, tekintse meg a [Kapcsolati problémák elhárítása](active-directory-aadconnect-troubleshoot-connectivity.md) szakaszt.
6. A Connect to AD DS (Csatlakozás az AD DS szolgáltatáshoz) képernyőn adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. A tartományrészt megadhatja NetBios- vagy FQDN-formátumban, vagyis FABRIKAM\rendszergazda vagy fabrikam.com\rendszergazda alakban. Kattintson a **Tovább** gombra.  
   ![Csatlakozás az AD DS szolgáltatáshoz](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. Az [**Azure AD bejelentkezés konfigurálása**](active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) lap csak akkor jelenik meg, ha nem végezte el [a tartományok ellenőrzését](../active-directory-domains-add-azure-portal.md) az [előfeltételek](active-directory-aadconnect-prerequisites.md) között.
   ![Nem ellenőrzött tartományok](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png)  
   Ha ez a lap megjelenik, tekintse át az összes **Not Added** (Hozzá nem adott) és **Not Verified** (Nem ellenőrzött) megjelöléssel rendelkező tartományt. Bizonyosodjon meg róla, hogy az Ön által használt tartományok ellenőrizve lettek az Azure AD szolgáltatásban. Miután ellenőrizte a tartományokat, kattintson a Frissítés szimbólumra.
8. A Ready to configure (Konfigurálásra kész) oldalon kattintson az **Install** (Telepítés) lehetőségre.
   * Másik lehetőségként a Ready to configure (Konfigurálásra kész) oldalon törölheti a **Start the synchronization process as soon as configuration completes** (Szinkronizálási folyamat indítása a konfiguráció befejeztével) beállítás bejelölését. A jelölőnégyzet jelölését akkor érdemes törölnie, ha további konfigurálást, például [szűrést](active-directory-aadconnectsync-configure-filtering.md) kíván végezni. Ha törli a beállítást, a varázsló konfigurálja a szinkronizálást, az ütemezőt azonban letiltva hagyja. Az ütemező nem fut, amíg manuálisan nem engedélyezi [a telepítővarázsló ismételt futtatásával](active-directory-aadconnectsync-installation-wizard.md).
   * Ha az Exchange működik a helyszíni Active Directory szolgáltatásban, akkor lehetősége van engedélyezni az [**Exchange hibrid telepítést**](https://technet.microsoft.com/library/jj200581.aspx) is. Ezt a beállítást akkor engedélyezze, ha Exchange postaládákat egy időben kíván üzemeltetni a felhőben és a helyszíni rendszeren.
     ![Készen áll az Azure AD Connect konfigurálására](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. A telepítés befejezése után kattintson az **Exit** (Kilépés) gombra.
10. Miután a telepítés befejeződött, jelentkezzen ki, majd ismét jelentkezzen be, mielőtt a Synchronization Service Managert (Szinkronizálási szolgáltatás kezelőjét) vagy a Synchronization Rule Editort (Szinkronizálási szabályok szerkesztőjét) használná.

## <a name="videos"></a>Videók
A gyorstelepítés használatát bemutató videót itt tekintheti meg:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
> 
> 

## <a name="next-steps"></a>Következő lépések
Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](active-directory-aadconnect-whats-next.md).

Ismerkedjen meg a következő, a telepítéssel engedélyezett szolgáltatásokkal: az [Automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md), a [Véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) és az [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).

Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](active-directory-aadconnectsync-feature-scheduler.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

## <a name="related-documentation"></a>Kapcsolódó dokumentáció
| Témakör |
| --- | --- |
| Az Azure AD Connect áttekintése |
| Telepítés testreszabott beállítások használatával |
| Frissítés a DirSync szolgáltatásról |
| Telepítési fiókok |

