---
title: Ismerje meg a legújabb Azure Vendégoperációsrendszer-kiadásainak listáját |} A Microsoft Docs
description: A legújabb kiadás híreket és SDK-kompatibilitási az Azure Cloud Services vendég operációs rendszer.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/13/2018
ms.author: raiye
ms.openlocfilehash: 239482151384ff555d86e3d639bfe1d75b0d0ceb
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604892"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Az Azure vendég operációs rendszerek kiadásai és SDK-kompatibilitási mátrixot
Itt, a legújabb Azure vendég operációs rendszer naprakész információkat és a Cloud Services-kiadások. Ez az információ segít a frissítési útvonal megtervezéséhez, mielőtt egy vendég operációs rendszer le van tiltva. Ha a szerepkörök használatára konfigurálja *automatikus* vendég operációs rendszer frissítése leírtak szerint [Azure vendég operációs rendszer frissítési beállítások][Azure Guest OS Update Settings], már nem létfontosságú, hogy elolvasta-e ezen a lapon.

> [!IMPORTANT]
> Ezen a lapon a Cloud Services webes és feldolgozói szerepkörök, amelyek egy vendég operációs rendszer épülve vonatkozik. Ugyanúgy **vonatkozik** iaas-beli virtuális gépeken.
>
>


> [!TIP]
>  Fizessen elő a [Vendég operációsrendszer-frissítés RSS-hírcsatorna] vendég operációs rendszer összes módosításáról a kellő időben értesítést szeretne kapni.
>
>

> [!IMPORTANT]
> Csak a 2 legújabb a vendég operációs rendszer támogatott és az Azure Portalon elérhető lesz.
>
>

A vendég operációs rendszer frissítése tesztadatkészletén? Ellenőrizze [ez] [ cloud updates] ki.

## <a name="news-updates"></a>Hírek

###### <a name="september-12-2018"></a>**2018. szeptember 12.**
Az augusztusi vendég operációs rendszer jelent meg.

###### <a name="august-3-2018"></a>**2018. augusztus 3.**
A július vendég operációs rendszer jelent meg.

###### <a name="july-3-2018"></a>**2018. július 3.**
A június vendég operációs rendszer jelent meg.

###### <a name="june-1-2018"></a>**2018. június 1.**
A lehetséges, hogy a vendég operációs rendszer jelent meg.

###### <a name="may-4-2018"></a>**2018. május 4.**
Az április vendég operációs rendszer jelent meg.

###### <a name="april-6-2018"></a>**2018. április 6.**
A március vendég operációs rendszer jelent meg.

###### <a name="march-19-2018"></a>**2018. március 19.**
A február vendég operációs rendszer jelent meg.

###### <a name="january-29-2018"></a>**2018. január 29.**
A január vendég operációs rendszer kiadása operációsrendszer-családok 2 (WA-VENDÉG-az operációs rendszer-2.70_201801-01) & 3 (WA-VENDÉG-operációsrendszer-3.57_201801-01)

###### <a name="january-4-2018"></a>**2018. január 4.**
A január vendég operációs rendszer kiadása az operációs rendszer családja 4-es (WA-VENDÉG-operációsrendszer-4.50_201801-01) & 5 (WA-VENDÉG-az operációs rendszer-5.15_201801-01) és fontos biztonsági javításokat tartalmaz.  

###### <a name="january-4-2018"></a>**2018. január 4.**
December vendég operációs rendszer jelent meg.

###### <a name="december-14-2017"></a>**2017. december 14.**
November vendég operációs rendszer jelent meg.

###### <a name="november-8-2017"></a>**2017. november 8.**
Október vendég operációs rendszer jelent meg.



## <a name="releases"></a>Kiadások
## <a name="family-5-releases"></a>5. családba tartozó kiadások
**Windows Server 2016**

.NET-keretrendszer telepítése: 4.0-s, 4.5-ös, 4.5.1, 4.5.2-es, 4.6, 4.6.1, 4.6.2-es

> [!NOTE]
> Az operációsrendszer-család 5 RDP-jelszónak legalább 10 karakterből kell lennie.
>

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum |
| --- | --- | --- |
| WA-VENDÉG-OPERÁCIÓSRENDSZER-5.22_201808-01 |2018. szeptember 12. |POST 5,24 |
| WA-VENDÉG-OPERÁCIÓSRENDSZER-5.21_201807-02 |2018. augusztus 3. |POST 5.23 |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-5.20_201806-01~~ |2018. július 3. |2018. szeptember 12. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-5.19_201805-01~~ |2018. június 1. |2018. augusztus 3. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-5.18_201804-01~~ |2018. május 4. |2018. július 3. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-5.17_201803-01~~ |2018. április 6. |2018. június 1.|
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-5.16_201802-01~~ |2018. március 12. |2018. május 4. |
|~~WA-GUEST-OS-5.15_201801-01~~ |2018. január 4. |2018. április 6. |
|~~WA-GUEST-OS-5.14_201712-01~~ |2018. január 4. |2018. március 12. |
|~~WA-GUEST-OS-5.13_201711-01~~ |2017. december 14. |2018. január 4.|
|~~WA-GUEST-OS-5.12_201710-02~~ |2017. november 8. |2018. január 4. |


## <a name="family-4-releases"></a>Család 4-kiadások
**Windows Server 2012 R2**

.NET-keretrendszer telepítése: 4.0-s, 4.5-ös, 4.5.1, 4.5.2

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum |
| --- | --- | --- |
| WA-VENDÉG-OPERÁCIÓSRENDSZER-4.57_201808-01 |2018. szeptember 12. |POST 4.59 |
| WA-VENDÉG-OPERÁCIÓSRENDSZER-4.56_201807-02 |2018. augusztus 3. |POST 4.58 |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-4.55_201806-01~~ |2018. július 3. |2018. szeptember 12. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-4.54_201805-01~~ |2018. június 1. |2018. augusztus 3. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-4.53_201804-01~~ |2018. május 4. |2018. július 3. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-4.52_201803-01~~ |2018. április 6. |2018. június 1. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-4.51_201802-01~~ |2018. március 12. |2018. május 4. |
|~~WA-GUEST-OS-4.50_201801-01~~ |2018. január 4. |2018. április 6. |
|~~WA-GUEST-OS-4.49_201712-01~~ |2018. január 4. |2018. március 12. |
|~~WA-GUEST-OS-4.48_201711-01~~ |2017. december 14. |2018. január 4. |
|~~WA-GUEST-OS-4.47_201710-02~~ |2017. november 8. |2018. január 4. |


## <a name="family-3-releases"></a>3-as család kiadások
**Windows Server 2012**

.NET-keretrendszer telepítése: 4.0-s, 4.5-ös, 4.5.1, 4.5.2

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum |
| --- | --- | --- |
| WA-VENDÉG-OPERÁCIÓSRENDSZER-3.64_201808-01 |2018. szeptember 12. |POST 3.66 |
| WA-VENDÉG-OPERÁCIÓSRENDSZER-3.63_201807-02 |2018. augusztus 3. |POST 3.65 |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-3.62_201806-01~~ |2018. július 3. |2018. szeptember 12. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-3.61_201805-01~~ |2018. június 1. |2018. augusztus 3. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-3.60_201804-01~~ |2018. május 4. |2018. július 3. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-3.59_201803-01~~ |2018. április 6. |2018. június 1. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-3.58_201802-01~~ |2018. március 19. |2018. május 4. |
|~~WA-GUEST-OS-3.57_201801-01~~ |2018. január 29. |2018. április 6. |
|~~WA-GUEST-OS-3.56_201712-01~~ |2018. január 4. |2018. március 19. |
|~~WA-GUEST-OS-3.55_201711-01~~ |2017. december 14. |2018. január 29. |
|~~WA-GUEST-OS-3.54_201710-02~~ |2017. november 8. |2018. január 4. |


## <a name="family-2-releases"></a>2 család kiadások
**Windows Server 2008 R2 SP1**

.NET-keretrendszer telepítése: 3.5, 4.0-s, 4.5-ös, 4.5.1, 4.5.2-es

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum |
| --- | --- | --- |
| WA-VENDÉG-OPERÁCIÓSRENDSZER-2.77_201808-01 |2018. szeptember 12. |POST 2.79 |
| WA-VENDÉG-OPERÁCIÓSRENDSZER-2.76_201807-02 |2018. augusztus 3. |POST 2.78 |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-2.75_201806-01~~ |2018. július 3. |2018. szeptember 12. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-2.74_201805-01~~ |2018. június 1. |2018. augusztus 3.|
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-2.73_201804-01~~ |2018. május 4. |2018. július 3. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-2.72_201803-01~~ |2018. április 6. |2018. június 1. |
|~~WA-VENDÉG-OPERÁCIÓSRENDSZER-2.71_201802-01~~ |2018. március 12. |2018. május 4. |
|~~WA-GUEST-OS-2.70_201801-01~~ |2018. január 29. |2018. április 6. |
|~~WA-GUEST-OS-2.69_201712-01~~ |2018. január 4. |2018. március 12. |
|~~WA-GUEST-OS-2.68_201711-01~~ |2017. december 14. |2018. január 29. |
|~~WA-GUEST-OS-2.67_201710-02~~ |2017. november 8. |2018. január 4. |
|~~WA-GUEST-OS-2.66_201709-01~~ |2017. október 6 |2017. december 14. |
|~~WA-GUEST-OS-2.65_201708-01~~ |2017. augusztus 24. |2017. december 14. |


## <a name="msrc-patch-updates"></a>MSRC-javítás frissítések
A lista minden egyes havi vendég operációs rendszer kiadása a javítások [Itt][patches].

## <a name="sdk-support"></a>SDK-támogatás
Annak ellenére, hogy a [kivezetési szabályzata, az Azure SDK] [ retire policy sdk] azt jelzi, hogy csak a fenti 2.2-es verzió támogatott, a megadott Vendég operációsrendszer-családok lehetővé teszik, hogy korábbi verzióit. Mindig a legújabb támogatott SDK-t használja.

| Vendég operációsrendszer-család | Kompatibilis SDK-verziókra |
| --- | --- |
| 5 |Verzió 2.9.5.1+ |
| 4 |2.1-es vagy újabb |
| 3 |1.8-as vagy újabb |
| 2 |1.3-as vagy újabb |
| 1 |1.0-s vagy újabb |

## <a name="guest-os-release-information"></a>Vendég operációs rendszer Kiadásadatok
Vannak a kiadások a vendég operációs rendszer számára fontos három dátumok: **kiadási** dátum, **le van tiltva** dátuma, és **lejárati** dátum. A vendég operációs rendszer elérhető számít, ha a portálon és a vendég operációs rendszer célként választható ki. Ha a vendég operációs rendszer elérte a **le van tiltva** dátuma, a rendszer eltávolítja az Azure. Minden olyan felhőalapú szolgáltatásnak, hogy a vendég operációs rendszer azonban továbbra is működik szokásos módon.

Az ablak között a **le van tiltva** dátum és a **lejárati** dátum biztosít a, a puffer egyszerűen átmenet a egy vendég operációs rendszer egy újabb. Ha használ *automatikus* , a vendég operációs rendszer, mindig is a legújabb verziót, és nem kell aggódnia, hamarosan lejár.

Ha a **lejárati** dátum továbbítja, minden olyan Felhőszolgáltatáshoz, továbbra is használja, hogy a vendég operációs rendszer leállította, törlődnek, vagy kényszerített frissítése. További információ a kivezetési szabályzata [Itt][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Vendég operációsrendszer-család-verzió magyarázata
A Vendég operációsrendszer-családok Microsoft Windows Server kiadott verzióján alapulnak. A vendég operációs rendszer az alapul szolgáló operációs rendszer, amely az Azure Cloud Services futtat. Minden egyes vendég operációs rendszer rendelkezik olyan család, verzióját és kiadási száma.

* **Vendég operációsrendszer-család**  
  A Windows Server operációs rendszer kiadás, amely egy vendég operációs rendszeren alapul. Ha például *3-as család* Windows Server 2012 rendszeren alapul.
* **A vendég operációs rendszer verziója**  
  Adott családba tartozó Vendég operációsrendszer-lemezképhez, valamint a megfelelő [Microsoft Security Response Center (MSRC)] [ msrc] javítást, a dátumot, a vendég operációs rendszer új verzió jön létre. Lehet, hogy nem minden javításokat tartalmaz.

    Számok számozása 0 és 1 növekmény minden alkalommal, amikor bekerül a frissítések új készletét. Záró nullák csak láthatók. Ha a fontos. Hogy a verzió 2.10 egy másik, sokkal újabb verziójú, mint a 2.1-es verzió.
* **Vendég operációsrendszer-kiadás**  
  Újbóli kiadása egy vendég operációs rendszer verziója. Újbóli akkor fordul elő, ha a Microsoft problémák talál; tesztelése során a változtatásokat. A legfrissebb kiadás mindig hatályon kívül helyez minden korábbi kiadásai, nyilvános vagy sem. Az Azure Portalon csak akkor engedélyezi a felhasználók számára egy adott verziójának legújabb kiadása. Egy előző kiadást futtató központi telepítések általában nem tartoznak kényszerített frissített függően a bejelentett hiba súlyosságát.

Az alábbi példában a 2 a család, 12 az verziója pedig "rel2" kiadása.

**Vendég operációs rendszer kiadási** – 2.12 rel2

**Ebben a kiadásban a konfigurációs karakterláncból** -WA-VENDÉG-operációsrendszer-2.12_201208-02

A vendég operációs rendszer konfigurációs karakterláncában együtt egy bemutató, mely MSRC javítások lettek figyelembe véve, kiadás dátuma, a beágyazott ugyanezeket az adatokat. Ebben a példában akár előállítása a Windows Server 2008 R2 és a köztük a 2012. augusztus MSRC javítások is venni. Csak a kifejezetten a Windows Server adott verziójával alkalmazása javításait is. Például egy MSRC javítás vonatkozik a Microsoft Office, ha azt nem lehet foglalt mert a termék már nem része a Windows Server alaplemezkép.

## <a name="guest-os-system-update-process"></a>Vendég operációs rendszer frissítési folyamat
Ezen a lapon a közelgő Vendégoperációsrendszer-kiadásainak listáját információkat biztosít. Ügyfelek azt jelezte, hogy szeretné-e tudja, mert azok a felhőszolgáltatásokhoz tartozó szerepkörök újraindul, ha a beállítás a "Automatikus" frissítési kiadási bekövetkezésekor. Kiadások a vendég operációs rendszer általában 2-3 hét a MSRC kiadás, amely minden hónap második keddjén frissítése után fordulhat elő. Új verziók tartalmazzák a megfelelő MSRC javításokat az egyes vendég operációsrendszer-család.

A Microsoft Azure folyamatosan frissítéseket ad ki. A vendég operációs rendszer csak egy ilyen frissítés folyamatban. A kiadás számos tényezőtől hatással lehet az itt felsorolt túl sok. Az Azure emellett több ezer gépet szó szerint több száz futtat. Ez azt jelenti, hogy nem lehetséges a pontos dátumot és időt, amikor a szerepkör újraindul. Dolgozunk a csomag korlátozható vagy újraindítások idő.

Ha a vendég operációs rendszer új kiadása közzé van téve, teljes mértékben Azure-ban propagálása időbe telhet. Szolgáltatás frissítve lett, hogy az új vendég operációs rendszer, mivel azok újraindulásakor érvényesítenie frissítési tartománnyal. Szolgáltatások "Automatikus" frissítések használatához állítsa a kiadási első fog kapni. A frissítés után látni fogja az új vendég operációs rendszer verzió a szolgáltatás az Azure Portalon ezeket tájékoztatási céllal felsorolja. Ebben az időszakban újból kiadott biztonsági frissítések fordulhat elő. Egyes verziói hosszabb idő alatt is üzembe helyezhetők, és az automatikus frissítési újraindítások nem fordulhat elő, a hivatalos kiadás dátumot követően számos hétig. A vendég operációs rendszer nem érhető el, ha explicit módon kiválaszthatja azt a verziót a portálon vagy a konfigurációs fájlban.

Ezután újraindul, és további információkat technikai részletek Vendég és gazdagép operációs rendszerének frissítések mutatók értékes információk nagy fokú, lásd az MSDN blogbejegyzésében [szerepkör példány újraindítása miatt operációs rendszer Verziófrissítései] [ restarts].

Ha manuálisan frissíti a vendég operációs rendszer, tekintse meg a [vendég operációs rendszer kivezetési szabályzata] [ retirepolicy] további információt.

## <a name="guest-os-supportability-and-retirement-policy"></a>Vendég operációs rendszer támogatási és kivezetési szabályzata
A vendég operációs rendszer támogatási és kivezetési szabályzat kifejtett [Itt][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Vendég operációsrendszer-frissítés RSS-hírcsatorna]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
