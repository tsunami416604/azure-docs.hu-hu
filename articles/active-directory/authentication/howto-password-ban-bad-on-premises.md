---
title: Az Azure AD-jelszó protection előzetes kiadásának telepítése
description: Az Azure AD jelszó protection előzetes kiadásának megtiltani rossz jelszavak a helyszíni központi telepítése
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 6fda373f832d6e24d1252587a19c88b0f464dda6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232345"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Előzetes verzió: Azure AD jelszóvédelem központi telepítése

|     |
| --- |
| Az Azure AD-jelszó protection és a tiltott egyénijelszó listájának az Azure Active Directory nyilvános előzetes verziójú funkciók. Az előzetes verziójú funkciók kapcsolatos további információkért lásd: [kiegészítő használati feltételek Microsoft Azure előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Most, hogy megismerhesse, [az Azure AD jelszavas védelmet a Windows Server Active Directory a kényszerítés módja](concept-password-ban-bad-on-premises.md), a következő lépés az, hogy tervezze meg, és a központi telepítés hajtható végre.

## <a name="deployment-strategy"></a>Központi telepítési stratégiája

A Microsoft azt javasolja, hogy a központi telepítési indítsa el a rendszervizsgálati módban. A rendszervizsgálati mód az alapértelmezett kezdeti beállítása ahol jelszavak továbbra is állítható be, és azokat, blokkolná-e létrehozni bejegyzéseket az eseménynaplóban talál. Amennyiben proxy kiszolgálókra és a tartományvezérlő ügynökök teljesen telepített rendszervizsgálati módban, regular figyelési el kell végezni annak meghatározására, milyen hatása jelszóházirend kényszerítési lenne a felhasználók és a környezet, ha a házirend kényszerítve lenne.

Az ellenőrzési szakaszban található a számos szervezet:

* Meglévő működési folyamatok biztonságosabb jelszavak használatát javítása van szükségük.
* Felhasználók által megszokott való nem biztonságos jelszavak rendszeresen kiválasztása
* A jövőbeli biztonsági kényszerítési, milyen hatással lehet rájuk, és segítséget nyújthatnak a támogatási jobb változást tisztában legyenek azzal, hogyan választhatnak a biztonságosabb jelszavak tájékoztatja van szükségük.

A szolgáltatás ésszerű ideje rendszervizsgálati módban fut, ha a kényszerítési konfiguráció a lehet tükrözni **naplózási** a **érvényesítése** ezáltal igénylő biztonságosabb jelszavak. Ebben az időszakban célzott figyelés érdemes.

## <a name="known-limitation"></a>Ismert korlátozása

Egy ismert korlátozás az Azure AD-jelszó védelmi proxy preview verzióban van. Többtényezős Hitelesítést igénylő bérlői rendszergazdai fiókok használata nem támogatott. Egy következő frissítés az Azure AD-jelszó védelmi proxy rendszergazdai fiókok, többtényezős Hitelesítést igénylő proxy regisztráció támogatja.

## <a name="single-forest-deployment"></a>Egyetlen erdő telepítése

Az Azure AD jelszóvédelem előnézete a proxy szolgáltatással akár két kiszolgálóra telepíti, és a DC-ügynökszolgáltatás fokozatosan telepítheti az Active Directory-erdő összes tartományvezérlőjére.

![Hogyan működnek együtt az Azure AD-jelszó védelmi összetevők](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>A szoftver letöltése

Nincsenek a két szükséges telepítők, amely letölthető az Azure AD jelszavas védelmet a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Az Azure AD jelszó védelmi proxy szolgáltatás telepítése és konfigurálása

1. Válassza ki az Azure AD-jelszó védelmi proxy szolgáltatás futtatásához egy vagy több kiszolgálót.
   * Minden ilyen szolgáltatás is csak jelszóházirendet egyetlen erdő esetén, és gazdaszámítógépen kell lennie a tartományhoz egy tartományhoz (gyökér- és gyermekszintű mindkét egyaránt támogatottak) az adott erdőben. Azure AD jelszó védelmi proxy szolgáltatás feladatát, teljesítéséhez nincs léteznie kell az erdő minden tartományban legalább egy tartományvezérlő, és az Azure AD jelszó védelmi Proxy gazdaszámítógépen közötti hálózati kapcsolat.
   * Telepítéséhez és futtatásához az Azure AD-jelszó védelmi proxy szolgáltatás egy tartományvezérlőn tesztelési célokra támogatott, de majd az internetkapcsolat szükséges.

   > [!NOTE]
   > A nyilvános előzetes legfeljebb két (2) proxykiszolgálók erdőnként támogat.

2. Szoftvertelepítés a jelszó Proxy szolgáltatás használatával az AzureADPasswordProtectionProxy.msi MSI-csomagot.
   * A szoftver telepítése nem igényel újraindítást. A Szoftvertelepítés előfordulhat, hogy automatizálható a szokásos eljárásokkal MSI, például: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

3. Nyissa meg rendszergazdaként egy PowerShell-ablakot.
   * Az Azure AD jelszóvédelem Proxy szoftver AzureADPasswordProtection nevű új PowerShell-modult tartalmaz. Az alábbi lépéseket a különböző parancsmagok futtatása a PowerShell-moduljának alapulnak, és azt feltételezik, hogy megnyitották egy új PowerShell-ablakot, és az alábbiak szerint importálta-e az új modul:
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > A telepítő szoftver módosítja a gazdaszámítógépen PSModulePath környezeti változó. Ahhoz, hogy ez a változás érvénybe lépéséhez, így a AzureADPasswordProtection powershell-modul importálható és használni esetleg nyisson meg egy teljesen új PowerShell konzol ablakot.

   * Ellenőrizze, hogy a szolgáltatás fut a következő PowerShell-paranccsal: `Get-Service AzureADPasswordProtectionProxy | fl`.
      * Az eredmény egy eredmény kell előállítania a **állapot** "Fut" eredményt ad vissza.

4. Regisztrálja a proxy.
   * 3. lépés befejezését követően az Azure AD-jelszó védelmi proxy szolgáltatás a számítógépen fut, de még nem rendelkezik a szükséges hitelesítő adatokat az Azure AD folytatott kommunikációhoz. Regisztráció az Azure AD szükséges, hogy képes használatával engedélyezze a `Register-AzureADPasswordProtectionProxy` PowerShell-parancsmagot. A parancsmaghoz szükséges a globális rendszergazdai hitelesítő adatait, az Azure-bérlőhöz, valamint a helyszíni Active Directory tartományi rendszergazdai jogosultságokkal az erdő gyökértartományában. Az egy adott proxykiszolgáló Service, a további indítások sikeres befejezése után `Register-AzureADPasswordProtectionProxy` továbbra is sikeres, de nem szükségesek.
      * A parancsmag a következőképpen futtatható:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         A példa csak akkor működik, ha az aktuálisan bejelentkezett felhasználó egyúttal a legfelső szintű tartomány Active Directory-tartományi rendszergazda. Helyett adja meg a szükséges tartományi hitelesítő adatokat a `-ForestCredential` paraméter.

   > [!TIP]
   > A jelentős késés előfordulhat (hány másodperc) Ez a parancsmag egy adott Azure-bérlő fut, a parancsmag végrehajtásának befejeződése előtt először. Ha hibát jelentett Ez a késés nem tekinthető riasztó.

   > [!NOTE]
   > Regisztráció az Azure AD jelszó védelmi proxy szolgáltatás várhatóan egyszeri lépés a szolgáltatás élettartama során. A proxy szolgáltatás automatikusan ettől kezdve minden egyéb szükséges maintainenance végez. Ha sikeres, hogy egy adott erdő, további indítások "Register-AzureADPasswordProtectionProxy", továbbra is sikeres, de nem szükségesek.

5. Az erdő regisztrálni.
   * Az Azure használatával folytatott kommunikációra a szükséges hitelesítő adatokkal a helyszíni Active Directory-erdő inicializálni kell a `Register-AzureADPasswordProtectionForest` Powershell-parancsmagot. A parancsmaghoz szükséges a globális rendszergazdai hitelesítő adatait, az Azure-bérlőhöz, valamint a helyszíni Active Directory tartományi rendszergazdai jogosultságokkal az erdő gyökértartományában. Ezt a lépést minden erdőre egyszer futtatnia.
      * A parancsmag a következőképpen futtatható:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         A példa csak akkor működik, ha az aktuálisan bejelentkezett felhasználó egyúttal a legfelső szintű tartomány Active Directory-tartományi rendszergazda. A másik lehetőség a szükséges tartományi hitelesítő adatok megadását a - ForestCredential paraméteren keresztül.

         > [!NOTE]
         > Ha több proxykiszolgálót a környezetben van telepítve, nem számít, mely adott proxykiszolgáló fenti a fenti eljárás végrehajtása során.

         > [!TIP]
         > A jelentős késés előfordulhat (hány másodperc) Ez a parancsmag egy adott Azure-bérlő fut, a parancsmag végrehajtásának befejeződése előtt először. Ha hibát jelentett Ez a késés nem tekinthető riasztó.

   > [!NOTE]
   > Az Active Directory-erdő regisztrációs várhatóan egyszeri lépés az erdő élettartama során. A tartomány a tartományvezérlő ügynökök fut az erdő automatikusan hajt végre semmilyen más szükséges maintainenance ettől kezdve. Az adott erdő, a további indítások sikeres befejezése után `Register-AzureADPasswordProtectionForest` továbbra is sikeres, de nem szükségesek.

6. Választható lehetőség: Konfigurálása az Azure AD jelszó védelmi proxy szolgáltatás egy adott portot figyeljen.
   * RPC TCP-n keresztül használják az Azure AD jelszóvédelem DC ügynökszoftver a tartományvezérlők kommunikálni az Azure AD-jelszó védelmi proxy szolgáltatás. Alapértelmezés szerint az Azure AD jelszóvédelem jelszó házirend Proxy szolgáltatás semmilyen elérhető dinamikus RPC-végpont figyel. Szükséges miatt a hálózati topológia vagy a tűzfallal kapcsolatos követelményeket, ha a szolgáltatás ehelyett beállítható egy adott TCP-port figyelésére.
      * A szolgáltatás futtatásához egy statikus port megadásához használja a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmag.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Érvényesítéséhez állítsa le és indítsa újra a szolgáltatást, a módosítások életbe léptetéséhez.

      * A szolgáltatás futtatásához egy dinamikus port megadásához használja ugyanezt az eljárást de nullára van állítva StaticPort vissza, például így:
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Érvényesítéséhez állítsa le és indítsa újra a szolgáltatást, a módosítások életbe léptetéséhez.

   > [!NOTE]
   > Az Azure AD-jelszó védelmi proxy szolgáltatás manuális újraindítását igényli, a portkonfigurációjának módosítását követően. Nincs szükség ilyen jellegű konfigurációs módosítások elvégzése után a tartományvezérlőkön futó tartományvezérlő ügynök szoftver újraindítására.

   * A jelenlegi konfiguráció, a szolgáltatás használatával lehet lekérdezni a `Get-AzureADPasswordProtectionProxyConfiguration` parancsmag az alábbi példában látható módon:

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Az Azure AD-jelszó DC védelmi ügynök szolgáltatás telepítése

* Telepítse az Azure AD jelszó DC védelmi ügynök szolgáltatás szoftver használatával a `AzureADPasswordProtectionDCAgent.msi` MSI-csomagot:
   * A szoftver telepítése a számítógép újraindítása a telepítéskor szükséges, és távolítsa el az operációsrendszer-követelményt, hogy jelszó-szűrő dll-fájl csak betöltése vagy a számítógép újraindítását követően a memóriából miatt.
   * A DC-ügynökszolgáltatás olyan gépen, amely még nincs egy tartományvezérlő telepítése esetén támogatott. Ebben az esetben a szolgáltatás elindul, és futtassa, de más módon lesz inaktívnak lennie, amíg után a gép van előléptették tartományvezérlővé.

   A Szoftvertelepítés előfordulhat, hogy automatizálható a szokásos eljárásokkal MSI, például:  `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > A példaparancs msiexec eredményezi egy azonnali újraindítás; Ez elkerülhető megadásával a `/norestart` jelzőt.

Miután egy tartományvezérlőre van telepítve, és újraindítása után, az Azure AD jelszóvédelem DC ügynök szoftver telepítése befejeződött. Szükséges, illetve lehetséges nem igényel további konfigurálást.

## <a name="multiple-forest-deployments"></a>Több erdő központi telepítések

Nincsenek további követelmények központi telepítése az Azure AD jelszavas védelem több erdők között. Minden olyan erdőben, egymástól függetlenül van konfigurálva, a egyerdős telepítése című részben leírtak szerint. Minden Azure AD jelszóvédelem Proxy csak a tartományhoz való erdőből származó tartományvezérlők támogat. Az Azure AD jelszó szoftver egy adott erdő nem észleli a másik erdő Active Directory megbízható konfigurációk függetlenül telepített szoftver az Azure AD jelszó.

## <a name="read-only-domain-controllers"></a>Írásvédett tartományvezérlők

Jelszó changes\sets soha nem dolgozza fel, megőrizve az írásvédett tartományvezérlők (RODC); Ehelyett a rendszer továbbítja írható tartományvezérlő. Ezért nincs szükség a tartományvezérlő ügynök szoftver telepítéséhez az RODC-k.

## <a name="high-availability"></a>Magas rendelkezésre állás

A fő aggodalma a magas rendelkezésre állás, az Azure AD a jelszavas védelem biztosítása a rendelkezésre állási a webproxy-kiszolgálók esetén egy erdő tartományvezérlőinek próbál töltse le az új házirendek vagy más adatokat az Azure-ból. A nyilvános előzetes verziója támogatja legfeljebb két proxykiszolgáló erdőnként. Minden egyes tartományvezérlő ügynök egyszerű ciklikus multiplexelés stílus algoritmust használ, mely proxykiszolgáló hívás és átugrása keresztül, amelyek nem válaszolnak proxykiszolgálókat meghatározásakor.
A szokásos magas rendelkezésre állással kapcsolatos problémákat a Tervező DC ügynökszoftver problémák elhárításáról. A DC-ügynök helyi gyorsítótárazza az nemrég letöltött jelszóházirendnek. Akkor is, ha a regisztrált proxykiszolgálókat bármilyen okból elérhetetlenné válik, a DC-ügynökök továbbra is a gyorsítótárazott jelszó házirendek kikényszerítéséhez. Nagy telepítések jelszóházirendek egy ésszerű frissítési gyakoriságát az általában a sorrendnek, nap, óra nem vagy kisebb.   A webproxy-kiszolgálók rövid kimaradások ezért nem indítják el az Azure AD-jelszó protection szolgáltatást vagy a biztonsági szempontból előnyökkel járhat működésének jelentős hatással.

## <a name="next-steps"></a>További lépések

Most, hogy az Azure AD jelszavas védelmet a helyszíni kiszolgálókon szükséges szolgáltatások telepítését végezze el a [utáni telepítése a konfigurációt és jelentési adatot összefog](howto-password-ban-bad-on-premises-operations.md) a központi telepítés befejezéséhez.

[Az Azure AD jelszóvédelem elméleti áttekintése](concept-password-ban-bad-on-premises.md)
