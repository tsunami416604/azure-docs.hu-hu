---
title: Az Azure Key Vault biztonságos |} A Microsoft Docs
description: Az Azure Key Vault, kulcsok és titkos kulcsok kezelése a key vault hozzáférési engedélyeinek kezelése. Hitelesítési és engedélyezési modellje a key vault és biztonságossá tétele a key vaultban.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 58307b25c03202fea63be25136c5a9c5a2d32473
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079038"
---
# <a name="secure-your-key-vault"></a>Kulcstartó védelme
Az Azure Key Vault egy felhőszolgáltatás, amely megvédi a titkosítási kulcsok és titkos kulcsokat (például a tanúsítványok, kapcsolati karakterláncok, jelszavak). Mivel ezek az adatok bizalmas, és az üzletileg kritikus, a kulcstartókhoz védelméhez, így csak a hitelesített alkalmazások és a felhasználók hozzáférést szerezni. 

Ez a cikk áttekintést a key vault hozzáférés modell. Hitelesítés és engedélyezés ismerteti, és a key vaulthoz való hozzáférés biztonságossá tételét írja le.

## <a name="overview"></a>Áttekintés
A kulcstartó-hozzáférés vezérlése két külön felületen, a felügyeleti síkon és az adatsíkon keresztül történik. Mindkét sík esetében megfelelő hitelesítésre és engedélyezésre szükség egy hívó (felhasználó vagy alkalmazás) kulcstartó hozzáférést kaphasson. Hitelesítés a hívó azonosítását létesít az engedélyezés pedig meghatározza a műveletek elvégzésére jogosult a hívó.

Hitelesítésre mind a felügyeleti sík, mind az adatsík az Azure Active Directoryt használja. Engedélyezésre a felügyeleti sík szerepköralapú hozzáférés-vezérlést (RBAC) használ, míg az adatsík kulcstartó-hozzáférési házirendet.

A tárgyalt témakörök rövid áttekintése:

[Hitelesítés az Azure Active Directory használatával](#authentication-using-azure-active-directory) – Ebből a fejezetből megtudhatja, hogyan végezhet hitelesítést a hívó az Azure Active Directoryval a felügyeleti síkon és az adatsíkon keresztüli kulcstartó-hozzáférés céljából. 

[Felügyeleti sík és adatsík](#management-plane-and-data-plane) – A felügyeleti sík és az adatsík a kulcstartó-hozzáféréséhez használt két hozzáférési síkot jelenti. Minden hozzáférési sík meghatározott műveleteket támogat. Ez a fejezet a hozzáférés végpontjait, a támogatott műveleteket és az egyes síkokon használt hozzáférés-vezérlés módját írja le. 

[Felügyeleti sík hozzáférés-vezérlése](#management-plane-access-control) – Ebben a fejezetben megtekintjük, hogyan engedélyezhetünk hozzáférést a felügyeleti sík műveletei számára szerepköralapú hozzáférés-vezérlés útján.

[Adatsík hozzáférés-vezérlése](#data-plane-access-control) – Ez a fejezet leírja, hogyan használható a kulcstartó-hozzáférési házirend az adatsík-hozzáférés vezérléséhez.

[Példa](#example) – Ez a példa azt ismerteti, hogyan állítsa be a hozzáférés-vezérlést, hogy három különböző csapat (biztonsági csapat, fejlesztők/operátorok és ellenőrök) fejlesztése, kezelhet és monitorozhat Azure-alkalmazásokhoz az adott feladatok elvégzéséhez a key vaultban.

## <a name="authentication-using-azure-active-directory"></a>Hitelesítés az Azure Active Directory használatával
Ha az Azure-előfizetésében kulcstartót hoz létre, azt automatikusan az előfizetéshez tartozó Azure Active Directory-bérlő társítva. Valamennyi hívót (felhasználót és alkalmazást) regisztrálni kell az ehhez a bérlőhöz, és a key vault eléréséhez hitelesítenie kell. Ez a követelmény vonatkozik mindkét felügyeleti sík és az adatsík-hozzáférés. Mindkét esetben kétféle módon férhet hozzá az alkalmazás a kulcstartóhoz:

* **felhasználó + alkalmazás-hozzáférés** – használható, ha az alkalmazást, hogy a key vault bejelentkezett felhasználó nevében. Az Azure PowerShell és az Azure Portalon az ilyen típusú hozzáférés példák. Hozzáférés biztosítása a felhasználók két módja van: 
- Hozzáférés biztosítása a felhasználók hozzáféréssel rendelkezzenek a key vault bármely alkalmazásból.
- A key vaulttal felhasználói hozzáférésének biztosítása csak (néven összetett identitás) egy adott alkalmazás használatakor.

* **csak az alkalmazásra vonatkozó hozzáférési** – démonszolgáltatásokat futtató vagy a háttérben futó feladatok alkalmazásokkal használatos. A kulcstartó-hozzáférés az alkalmazásidentitás számára engedélyezett.

Az Azure Active Directory-hitelesítés és -tokenbeolvasás az alkalmazás mindkét típusa esetén elvégezhető a [támogatott hitelesítési módszerek](../active-directory/develop/authentication-scenarios.md) bármelyikével. A hitelesítés módszere az alkalmazás típusától függ. Az alkalmazás a későbbiekben ezt a tokent használja, amikor REST API-kérelmet küld a kulcstartónak. Felügyeleti sík kérelmek egy Azure Resource Manager végpontjához kerülnek. Adatsík-hozzáférés esetén az alkalmazások közvetlenül a kulcstartó végpontjával kommunikálnak. További részletek a [teljes hitelesítési folyamatról](../active-directory/develop/v1-protocols-oauth-code.md). 

Annak az erőforrásnak a neve, melyhez az alkalmazás tokent kér, attól függően változik, hogy az alkalmazás felügyeleti vagy adatsíkhoz készül hozzáférni. Így az erőforrásnév vagy felügyeleti sík, vagy adatsík végpontja, amint az egy később szakasz táblázatában szerepel, az Azure-környezettől függően.

Ha mind a felügyeleti síkra, mind az adatsíkra egyetlen hitelesítési mechanizmust alkalmaz, annak megvannak a maga előnyei:

* A szervezetek központilag vezérelhetik valamennyi kulcstartójukat
* Ha egy felhasználó távozik, azonnal elveszíti a szervezeten belüli összes kulcstartó-hozzáférését
* A szervezetek az Azure Active Directory beállításain keresztül testre szabhatják a hitelesítést (például Multi-Factor Authentication engedélyezésével fokozhatják a biztonságot)

## <a name="management-plane-and-data-plane"></a>Felügyeleti sík és adatsík
Az Azure Key Vault az Azure-szolgáltatások az Azure Resource Manager-alapú üzemi modellben érhetőek. Egy kulcstartót hoz létre, kap egy virtuális tároló például a kulcsok, titkos kódok és tanúsítványok bizalmas objektumok tárolására. Meghatározott műveleteket a key vault használatával a felügyeleti sík és az adatsík felületei. A felügyeleti sík a key vault kezelésére szolgál. Ez magában foglalja a műveleteket, például attribútumok kezelése és az adatsík az adathozzáférési házirendek beállítása. Az adatok sík felületén hozzáadása, törlése, módosítása és használja a kulcsok, titkos kulcsok és a key vaultban tárolt tanúsítványok segítségével.

A felügyeleti sík és az adatsík felületei az alább felsorolt eltérő végpontokon keresztül érhetők el. A második oszlopa ismerteti a DNS-neveit különböző Azure-környezetekben ezeket a végpontokat. A harmadik oszlop az egyes hozzáférési síkokról elvégezhető műveleteket ismerteti. Minden hozzáférési sík is rendelkezik a saját hozzáférés-vezérlési mechanizmus. Felügyeleti sík hozzáférés-vezérlés síknak hozzáférés-vezérlés (RBAC) használatával van beállítva. Adatsík-hozzáférés vezérlése a kulcstartó hozzáférési házirendjének használatával van beállítva.

| Hozzáférési sík | Hozzáférés végpontjai | Műveletek | Hozzáférés-vezérlési mechanizmus |
| --- | --- | --- | --- |
| Felügyeleti sík |**Globálisan:**<br> management.azure.com:443<br><br> **Az Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |Kulcstartó létrehozása/olvasása/frissítése/törlése <br> Kulcstartó hozzáférési házirendjeinek beállítása<br>Címkék beállítása kulcstartóhoz |Az Azure Resource Manager szerepköralapú hozzáférés-vezérlése (RBAC) |
| Adatsík |**Globálisan:**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Az Azure China 21Vianet:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 |Kulcsok: Visszafejtés, Titkosítás, UnwrapKey, WrapKey, Ellenőrzés, Aláírás, Beolvasás, Listázás, Frissítés, Létrehozás, Importálás, Törlés, Biztonsági mentés, Visszaállítás<br><br> Titkos kulcsok: Beolvasás, Listázás, Beállítás, Törlés |Kulcstartó hozzáférési házirendje |

Felügyeleti sík és az adatsík hozzáférés-vezérlése egymástól függetlenül működik. Például ha szeretne hozzáférést biztosítani egy alkalmazás-kulcsok használata a key vaultban, akkor csak kell adatsík-hozzáférés biztosítása. Hozzáférés a kulcstartó-hozzáférési házirendek. Egy felhasználónak, el kell olvasnia a tároló tulajdonságait és címkék, de nem érheti el adatait (kulcsok, titkos kódok vagy tanúsítványokat), ezzel szemben, csak adatsík-hozzáférés vezérlése van szüksége. Hozzáférés hozzárendelése "olvasási" hozzáférést RBAC használatával, a felhasználó által.

## <a name="management-plane-access-control"></a>Felügyeleti sík hozzáférés-vezérlése
A felügyeleti sík olyan műveleteket tartalmaz, amelyek hatással vannak a key vaultban, például:

- Létrehozás, vagy törölje a key vault.
- Bevezetés a tárolók listáját az előfizetéshez.
- A key vault tulajdonságait (például cikkszám, címkék) beolvasása.
- Elérő felhasználói vezérlő és a key vault hozzáférési házirendeket állíthat be a kulcsok és titkos kulcsok.

A felügyeleti sík hozzáférés-vezérlése RBAC használatával történik. Tekintse meg az előző fejezet táblázatában a felügyeleti síkon keresztül elvégezhető kulcstartó-műveletek teljes listáját. 

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
Minden Azure-előfizetés Azure Active Directoryval rendelkezik. Ezen könyvtár valamennyi felhasználója, csoportja és alkalmazása hozzáférést kaphat Azure-előfizetése Azure Resource Manager-alapú üzemi modellt használó erőforrásainak kezeléséhez. Az ilyen típusú hozzáférés-vezérlést szerepköralapú hozzáférés-vezérlésnek (RBAC-nek) nevezzük. A hozzáférés az [Azure Portal](https://portal.azure.com/), az [Azure Parancssorifelület-eszközök](../cli-install-nodejs.md), a [PowerShell](/powershell/azureps-cmdlets-docs) vagy az [Azure Resource Manager REST API-k](https://msdn.microsoft.com/library/azure/dn906885.aspx) használatával kezelhető.

Az erőforrás csoport és hozzáférés a felügyeleti sík, az Azure Active Directoryval, hozzon létre egy kulcstartót. Adhat például felhasználóknak vagy egy csoport egy erőforráscsoport kulcstartóit kezeljék lehetővé teszi.

Felhasználók, csoportok és alkalmazások egy adott hatókörnél hozzáférést biztosíthat a megfelelő RBAC-szerepkörök hozzárendelésével. Például hozzáférést biztosítani egy felhasználónak kulcstartóit kezeljék, hozzárendelhet egy előre meghatározott "Key Vault-közreműködő" szerepkört a felhasználóhoz egy adott hatókörnél. A hatókör ebben az esetben lenne egy előfizetés, erőforráscsoport vagy egy adott kulcstartó. Az előfizetés-szinten hozzárendelt szerepkör az adott előfizetésen belül működő összes erőforráscsoportra és erőforrásra vonatkozik. Az erőforráscsoport-szinten hozzárendelt szerepkör az adott erőforráscsoport valamennyi erőforrására vonatkozik. Egy adott erőforráshoz rendelt szerepkör adott erőforrásra vonatkozik. Számos előre definiált szerepkörök állnak rendelkezésre (lásd: [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md)). Ha egy előre meghatározott szerepkör nem fér el az igényeinek, megadhatja saját szerepkört.

> [!IMPORTANT]
> Vegye figyelembe: ha a kulcstartó felügyeleti síkjához a felhasználónak közreműködői engedélye (RBAC) van, hozzáférést engedélyezhet magának az adatsíkhoz is az adatsík-hozzáférést vezérlő kulcstartó-hozzáférési házirend beállításával. Ezért javasoljuk, hogy tartsa szigorú ellenőrzés alatt, ki rendelkezhet „közreműködői” hozzáféréssel kulcstartóihoz, így csak az arra jogosult személyek érhetik el, illetve kezelhetik kulcstartóit, kulcsait, titkos kulcsait és tanúsítványait.
> 
> 

## <a name="data-plane-access-control"></a>Adatsík-hozzáférés vezérlése
A Key vault adatok síkjával végzett műveletek tárolt objektumokat, például a kulcsok, titkos kódok és tanúsítványok érvényesek. Kulcsműveletek például létrehozása, importálása, frissítése, listázása, biztonsági mentése és állítsa vissza a kulcsokat. Titkosítási műveletek kereszt, győződjön meg arról, titkosítása, visszafejtése, wrap, kicsomagolása, állítsa be a címkék és egyéb attribútumok kulcsok. Hasonlóképpen, titkos műveletek közé tartozik, get, set, list, törölje.

Az adatsík-hozzáférés a kulcstartó hozzáférési házirendjeinek beállításán keresztül biztosítható. Ahhoz, hogy egy felhasználó, csoport vagy alkalmazás hozzáférési házirendet tudjon beállítani egy adott kulcstartóhoz, rendelkeznie kell a kulcstartó felügyeleti síkjának közreműködői engedélyeivel (RBAC). A felhasználók, csoportok és alkalmazások számára hozzáférés engedélyezhető egy adott kulcstartó kulcsaihoz vagy titkos kulcsaihoz kapcsolódó, meghatározott műveletek elvégzéséhez. Akár 1024 hozzáférésiszabályzat-bejegyzés támogatása kulcstartók esetében. Azure Active Directory biztonsági csoport létrehozásával és felhasználók felvételével a csoportba több felhasználó számára biztosíthat adatsíkszintű kulcstartó-hozzáférést.

### <a name="key-vault-access-policies"></a>Kulcstartó-hozzáférési házirendek
A kulcstartó-hozzáférési házirendekkel külön engedélyezheti a kulcsok, titkos kódok és tanúsítványok. Adhat például a felhasználói hozzáférés kizárólag a kulcsokhoz és a titkos kulcsokhoz nem. Engedélyeket az eléréséhez, kulcsok vagy titkos kulcsok vagy tanúsítványok olyan tárolószinten engedélyezett. Kulcstartó-hozzáférési házirend nem támogatja az oszlopszintű engedélyek részletes objektum, például egy adott kulccsal és titokkal/tanúsítvánnyal. Kulcstartó-hozzáférési házirendeket az [Azure portal](https://portal.azure.com/), az [Azure parancssorifelület-eszközök](../cli-install-nodejs.md), a [PowerShell](/powershell/azureps-cmdlets-docs) vagy a [Kulcstartókezelési REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx) használatával állíthat be.

> [!IMPORTANT]
> Vegye figyelembe, hogy a kulcstartó-hozzáférési házirendek tárolószinten érvényesek. Például, ha egy felhasználó engedélyt kap kulcsok létrehozására és törlésére, az adott kulcstartó összes kulcsa esetén végrehajthatja ezeket a műveleteket.

Az adatsík elérése hozzáférési szabályzatok használatán kívül [az Azure Key Vault virtuális hálózati szolgáltatásvégpontjaival](key-vault-overview-vnet-service-endpoints.md) is korlátozható [tűzfalak és virtuális hálózati szabályok](key-vault-network-security.md) konfigurálásával, amely egy további biztonsági réteget biztosít.

## <a name="example"></a>Példa
Tegyük fel, adatokat és az aláírási műveletekhez RSA 2048 bites kulcs tárolására egy tanúsítványt az SSL-hez, az Azure storage használó alkalmazás fejlesztésén. Tegyük fel, ez az alkalmazás fut az Azure virtuális gépként (vagy egy virtuálisgép-méretezési csoportban). A key vault használata az összes titkos Alkalmazáskulcs tárolására, és az Azure Active Directory-hitelesítést az alkalmazás által használt azon rendszerindítási tanúsítvány tárolására.

Íme a kulcsok és titkos kulcsok a key vaultban tárolt típusú összefoglalása:

* **SSL-tanúsítvány** – SSL-hez használatos
* **Tárkulcs** – tárfiók eléréséhez használatos
* **RSA 2048 bites kulcsot** – az aláírási műveletekhez használatos
* **Rendszerindítási tanúsítvány** – az Azure Active Directoryval hitelesíti. Miután engedélyezett hozzáférést a rendszer beolvassa a tárfiók kulcsát, és használja az RSA-kulcsot az aláíráshoz.

Most nézzük meg, kik felügyelete, telepítése és naplózzák ezt az alkalmazást. Ebben a példában három szerepkört használunk.

* **Biztonsági csapat** -általában informatikai munkatársak az irodából a CSO (biztonsági vezető), illetve az ezekkel egyenértékű. Ez a csapat felelős a megfelelő személyek, a titkos kulcsok. Például SSL-tanúsítványok, kapcsolati karakterláncok, aláíráshoz használt RSA-kulcsok és a tárfiók kulcsait.
* **A fejlesztők/operátorok** -azok számára, akik fejlesztette az alkalmazást, és ezután telepítheti az Azure-ban. Általában ezek még nem része a biztonsági csapatnak, és ezért azok nem fér hozzá a bizalmas adatok, például SSL-tanúsítványok és az RSA-kulcsok. Csak az általuk üzembe helyezett alkalmazásnak hozzá kell férniük azokat az objektumokat.
* **Ellenőrök** – általában külön csoport, akik, fejlesztők és általános informatikai személyzettől elkülönítve. Felelősségét, hogy tekintse át a használatának és karbantartásának tanúsítványok, kulcsok és titkos kulcsok biztonsági szabványoknak való megfelelőség érdekében. 

Egy szerepkör, amely itt nem foglalkozik az alkalmazás, de Fontos megemlíteni van. A szerepkör az előfizetés (vagy erőforráscsoportot) rendszergazda. Az előfizetés-rendszergazda beállítja a kezdeti hozzáférési engedélyeket a biztonsági csapatnak. Az előfizetés-rendszergazda hozzáférést ad a biztonsági csapatnak, használja egy erőforráscsoport, amely tartalmazza az alkalmazás által igényelt erőforrásokra.

Most nézzük meg, milyen műveletet hajtanak végre az egyes szerepkörök a jelen alkalmazás összefüggésében.

* **Biztonsági csapat**
  * Kulcstartók létrehozása
  * Kulcstartónaplózás bekapcsolása
  * Kulcsok/titkos kulcsok hozzáadása
  * Kulcsok biztonsági másolatának létrehozása vészhelyreállításhoz
  * Kulcstartó-hozzáférési házirend beállítása adott műveletek felhasználók és alkalmazások általi végrehajtásának engedélyezéséhez
  * Kulcsok/titkos kulcsok időnkénti összegzése
* **Fejlesztők/operátorok**
  * Biztonsági csapat rendszerindítási és SSL-tanúsítványok (ujjlenyomatok), tárkulcs (titkos URI) és aláírási kulcs (kulcs URI) való hivatkozás
  * A kulcsokhoz és titkos kulcsokhoz programozott módon hozzáférő alkalmazás fejlesztése és üzembe helyezése
* **Ellenőrök**
  * Ellenőrzik a használati naplókat, és megerősítik, a helyes kulcsokat/titkos kulcsokat használták-e az adatbiztonsági szabványoknak megfelelően

Most nézzük meg, milyen hozzáférési engedélyekre szükség minden egyes szerepkör és az alkalmazás hozzárendelt feladataik elvégzéséhez. 

| Felhasználói szerepkör | Felügyeleti sík engedélyei | Adatsík engedélyei |
| --- | --- | --- |
| Biztonsági csapat |Kulcstartó-közreműködő |Kulcsok: biztonsági mentése, létrehozása, törlése, beolvasása, importálása, listázása, visszaállítása <br> Titkos kulcsok: összes |
| Fejlesztők/operátor |kulcstartó-üzembehelyezési engedély, hogy az általuk üzembe helyezett virtuális gépek lehívhassák a kulcstartóból a titkos kulcsokat |None |
| Ellenőrök |None |Kulcsok: listája<br>Titkos kulcsok: listája |
| Alkalmazás |None |Kulcsok: aláírása<br>Titkos kulcsok: beolvasása |

> [!NOTE]
> Az ellenőröknek listázniuk kell a kulcsok és titkos kulcsok engedélyeit, hogy kivizsgálhassák azon kulcsok és titkos kulcsok attribútumait, amelyek nem kerülnek naplózásra, például címkék, aktiválási és lejárati dátumok.
> 
> 

A key vault-engedélyek, valamint mindhárom szerepkörnek is kell más erőforrásokhoz való hozzáférést. Például azért, hogy üzembe tudják helyezni a virtuális gépeket (vagy webes alkalmazásokat stb.) A fejlesztőknek/operátoroknak is ilyen „közreműködői” hozzáférésre van szükségük az adott erőforrástípusokhoz. Ellenőrök kell "olvasási" hozzáférést a tárfiókhoz a key vault naplóinak tárolására.

A lépéseknek az ismertetése, ez a cikk a biztonságossá tétele a key vaulthoz való hozzáférés, mivel azt a témával kapcsolatos fogalmak csak ismertetéséhez. Tanúsítványok központi telepítése, elérése programozott módon a kulcsok és titkos kulcsok és egyéb részleteket máshol terjed ki. Például:

- Virtuális gépek a kulcstartóban tárolt tanúsítványok telepítését tárgyalja a [blogbejegyzést: üzembe helyezése tanúsítványok virtuális gépekhez ügyfél által felügyelt Key vaultból](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)
- A [Azure Key Vault ügyfél minták letöltése](https://www.microsoft.com/download/details.aspx?id=45343) egy rendszerindítási tanúsítvány használatának hitelesítéséhez az Azure AD részére egy kulcstartó eléréséhez.

A hozzáférési engedélyeket a legtöbb Azure portal használatával is megadható. Részletes engedélyezés, szükség lehet az Azure PowerShell vagy parancssori felület használatával a kívánt eredmények eléréséhez. 

A következő PowerShell-kódtöredékek esetén a következőket vesszük alapul:

* Az Azure Active Directory-rendszergazda hozott létre biztonsági csoportokat, amelyek a (a Contoso biztonsági csapat, Contoso alkalmazás fejlesztői és a Contoso alkalmazás ellenőrei) három szerepkört képviselik. A rendszergazda felhasználókat is hozzáadott azokhoz a csoportokhoz, amelyekbe tartoznak.
* **ContosoAppRG**: az az erőforráscsoport, amelyben minden erőforrás megtalálható. **contosologstorage**: a naplók tárolási helye. 
* A **ContosoKeyVault** kulcstartónak és a kulcstartónaplókhoz használt **contosologstorage** tárfióknak az Azure rendszer egyazon helyén kell lennie

Az előfizetés-rendszergazda először „kulcstartó-közreműködő” és „felhasználóihozzáférés-rendszergazda” szerepkört rendel hozzá a biztonsági csapathoz. Ezek a szerepkörök lehetővé teszik a biztonsági csoport más erőforrásokhoz való hozzáférés kezelése, valamint a ContosoAppRG erőforráscsoport kulcstartóit kezeljék.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

A következő parancsfájl bemutatja, hogyan a biztonsági csapat hozzon létre egy kulcstartót, és állítsa be a naplózást és a hozzáférési engedélyeket. Lásd: [kapcsolatos Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok](about-keys-secrets-and-certificates.md) részleteiről a Key Vault hozzáférési házirend engedélyek.

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Az egyéni szerepkör, meghatározása után, csak ahhoz az előfizetéshez rendelhető hozzá, amelyben a ContosoAppRG erőforráscsoportot létrehozták. Ha ugyanazon egyéni szerepköröket más előfizetések más projektekhez használható, annak hatóköréhez több előfizetést is hozzáadtak.

Az erőforráscsoport a fejlesztők/operátorok "üzembe helyezés/művelet" engedélyt az egyéni szerepkör-hozzárendelés hatókörét. Ez lehetővé teszi, hogy csak virtuális gépek hozzáférjenek a titkos kulcsokat (SSL-tanúsítványt és a rendszerindítási tanúsítványt) a "ContosoAppRG" erőforráscsoportban létrehozott. A fejlesztők/operátorok csapatának tagja által másik erőforráscsoportban létrehozott virtuális gépek nem kell a titkos kódokhoz való hozzáférés, még akkor is, ha rendelkezik a titkos URI-k.

Ez a példa bemutatja egy egyszerű forgatókönyvet. Lehet, hogy a valós életbeli forgatókönyvek összetettebb, és szükség lehet a kulcstartóengedélyek igényei alapján a key vaultban. Ebben a példában feltételezzük, a kulcs- és titkoskulcs-referenciákat (URI-k és ujjlenyomatok), amely a fejlesztők/operátorok biztosítani az alkalmazásokban hivatkoznia kell a biztonsági csapat adja. A fejlesztők/operátorok nem igényelnek semmilyen adatsík-hozzáférést. Ebben a példában legfőképpen a kulcstartó biztonságossá tenni. Hasonló figyelmet kell fordítani biztonságos [a virtuális gépek](https://azure.microsoft.com/services/virtual-machines/security/), [tárfiókok](../storage/common/storage-security-guide.md), és más Azure-erőforrások.

> [!NOTE]
> Megjegyzés: Ez a példa bemutatja, hogyan lesz zárolva a kulcstartó-hozzáférés üzemi környezetben. A fejlesztőknek saját előfizetéssel vagy erőforráscsoporttal kell rendelkezniük, ahol teljes körű engedélyekkel rendelkeznek kulcstartóik, virtuális gépeik és a tárfiókjuk kezelésére az alkalmazás fejlesztésének helyén.

Erősen javasoljuk, hogy biztonságos hozzáférést a kulcstartóhoz továbbá által [konfigurálása a Key Vault-tűzfalak és virtuális hálózatok](key-vault-network-security.md).

## <a name="resources"></a>További források
* [Azure Active Directory szerepköralapú hozzáférés-vezérlése](../role-based-access-control/role-assignments-portal.md)
  
  Ez a cikk az Azure Active Directory szerepkörön alapuló hozzáférés-vezérlését és annak működési módját ismerteti.
* [RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md)
  
  Ez a cikk az RBAC-ben elérhető összes beépített szerepkört ismerteti.
* [A Resource Manager-alapú és a klasszikus üzembe helyezés ismertetése](../azure-resource-manager/resource-manager-deployment-model.md)
  
  Ez a cikk a Resource Manager-alapú és a klasszikus üzembe helyezési modelleket ismerteti, és bemutatja az erőforrás-kezelő és az erőforráscsoportok használatának előnyeit
* [Szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell-lel](../role-based-access-control/role-assignments-powershell.md)
  
  Ez a cikk azt ismerteti, hogyan kezelheti a szerepköralapú hozzáférés-vezérlést Azure PowerShell-lel
* [Szerepköralapú hozzáférés-vezérlés kezelése REST API-val](../role-based-access-control/role-assignments-rest.md)
  
  Ez a cikk leírja, hogyan használható a REST API az RBAC kezeléséhez.
* [Szerepköralapú hozzáférés-vezérlés az Ignite-tól a Microsoft Azure számára](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  A 2015 Microsoft Ignite konferencia videó ismerteti, hozzáférés-kezelési és jelentéskészítési lehetőségeket az Azure-ban. Azt is bemutatja az Azure-előfizetések az Azure Active Directoryval való hozzáférés biztonságossá tételének ajánlott eljárásait.
* [Hozzáférés engedélyezése webalkalmazásoknak OAuth 2.0 és az Azure Active Directory használatával](../active-directory/develop/v1-protocols-oauth-code.md)
  
  A cikk az Azure Active Directoryval történő hitelesítés teljes OAuth 2.0-s folyamatát ismerteti.
* [Kulcstartókezelési REST API-k](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Ez a dokumentum referenciaként szolgál a REST API-k számára a kulcstartó programozott kezeléséhez, a kulcstartó-hozzáférési házirend beállításával együtt.
* [kulcstartó REST API-k](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  A kulcstartó REST API referenciadokumentációjára mutató hivatkozás.
* [Kulcshozzáférés-vezérlés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  A titkoskulcshozzáférés-vezérlés referenciadokumentációjára mutató hivatkozás.
* [Titkoskulcs-hozzáférés vezérlése](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  A kulcshozzáférés-vezérlés referenciadokumentációjára mutató hivatkozás.
* Kulcstartó-hozzáférési házirend [beállítása](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) és [eltávolítása](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) PowerShell használatával
  
  PowerShell-parancsmagok kulcstartó-hozzáférési házirend kezeléséhez szükséges referenciadokumentációjára mutató hivatkozások.

## <a name="next-steps"></a>További lépések
[Key Vault-tűzfalak és virtuális hálózatok konfigurálása](key-vault-network-security.md)

A rendszergazdáknak szóló bevezető oktatóanyag a [Bevezetés az Azure Key Vault használatába](key-vault-get-started.md) című cikkben érhető el.

A kulcstartó használatának naplózásával kapcsolatos további információkért tekintse meg [Az Azure Key Vault naplózása](key-vault-logging.md) című cikket.

A kulcsok és a titkos kulcsok Azure Key Vaulttal történő használatával kapcsolatos további információkért tekintse meg az [About Keys and Secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx) (Kulcsok és titkos kulcsok) című cikket.

Amennyiben a kulcstartóval kapcsolatban kérdése merülne fel, tekintse meg az [Azure Key Vault fórumait](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

