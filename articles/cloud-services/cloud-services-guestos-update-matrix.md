---
title: További tudnivalók a legújabb Azure vendég operációs rendszereinek kiadásait |} Microsoft Docs
description: A legújabb kiadás híreket és SDK való kompatibilitást, Azure Cloud Services vendég operációs rendszer.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 4/6/2018
ms.author: raiye
ms.openlocfilehash: b4206d90ddd65f58bcdb92661f64d005a5e40563
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Az Azure vendég operációs rendszereinek kiadásait és SDK-kompatibilitási mátrix
Itt való naprakész információkat a legújabb Azure vendég operációs rendszer feloldja a Felhőszolgáltatásai számára. Ez az információ segít a frissítési lépések megtervezéséről, mielőtt egy vendég operációs rendszer le van tiltva. Ha a szerepkörök használatára konfigurálja *automatikus* vendég operációs rendszer frissíti a [Azure vendég operációs rendszer frissítési beállítások][Azure Guest OS Update Settings], nem elengedhetetlen, hogy olvassa el ezen a lapon.

> [!IMPORTANT]
> Ezen az oldalon Felhőszolgáltatás webes és feldolgozói szerepkörök, amelyek egy vendég operációs rendszer fut vonatkozik. Létezik **nem alkalmazható** infrastruktúra-szolgáltatási virtuális gépeket.
>
>


> [!TIP]
>  Fizessen elő a [vendég operációs rendszer frissítési RSS-hírcsatorna] a vendég operációs rendszer összes módosítás mindig a kellő időben értesítést szeretne kapni.
>
>

> [!IMPORTANT]
> A November bevezetés kiindulva, csak a vendég operációs rendszer legújabb 2 verziókat lesz támogatott, és elérhető az Azure portálon.
>
>

Nem tudja, hogy milyen a vendég operációs rendszer, vagy hogyan a vendég operációs rendszer feloldja a munkát? Olvasási [ez](#how-it-works) szakasz.

## <a name="news-updates"></a>Hírek
###### <a name="april-6-2018"></a>**2018. április 6.**
A március vendég operációs rendszer adott ki.

###### <a name="march-19-2018"></a>**2018. március 19.**
A február vendég operációs rendszer adott ki.

###### <a name="january-29-2018"></a>**2018. január 29.**
A január vendég operációs rendszer kiadása az operációsrendszer-családok 2 (WA-VENDÉG-OS-2.70_201801-01) & 3 (WA-VENDÉG-operációsrendszer-3.57_201801-01)

###### <a name="january-4-2018"></a>**2018. január 4.**
Az operációs rendszer családok 4-es kiadása a január vendég operációs rendszer (WA-VENDÉG-operációsrendszer-4.50_201801-01) & 5 (WA-VENDÉG-az operációs rendszer-5.15_201801-01) és fontos biztonsági javításokat tartalmaz.  

###### <a name="january-4-2018"></a>**2018. január 4.**
December vendég operációs rendszer adott ki.

###### <a name="december-14-2017"></a>**2017. december 14.**
November vendég operációs rendszer adott ki.

###### <a name="november-8-2017"></a>**2017. november 8.**
Október vendég operációs rendszer adott ki.

###### <a name="october-6-2017"></a>**2017. október 6.**
Szeptembertől vendég operációs rendszer adott ki. A Windows Server 2016. szeptemberi kiadás netfx3 alapértelmezés szerint engedélyezve van. Az ügyfelek hozzá kell adnia "dism / online Feature /featurename:netfx3" a saját OnStart, ha a munkafolyamat megköveteli őket egy 4.x futtatási idő mellett .NET 2.x alkalmazás futtatására, vagy ha a .NET-2.x alkalmazás futtatásakor kezelését a hiba, és majd futtatta a .NET-4.x-es alkalmazás.

###### <a name="september-14-2017"></a>**2017. szeptember 14.**
Szeptembertől vendég operációs rendszer bevezetés szeptember 14 indul, és a tervezett kiadását. október 9 rendelkezik.

###### <a name="august-24-2017"></a>**2017. augusztus 24.**
Augusztus vendég operációs rendszer adott ki.

###### <a name="august-3-2017"></a>**2017. augusztus 3.**
Július vendég operációs rendszer adott ki.

###### <a name="july-19-2017"></a>**2017. július 19.**
Július vendég operációs rendszer bevezetés július 19 indul, és a tervezett kiadását augusztus 8 rendelkezik.


## <a name="releases"></a>Kiadások
## <a name="family-5-releases"></a>Feloldja a családja 5
**Windows Server 2016**

.NET-keretrendszer telepített: 4.0-s, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2-es verziójához

> [!NOTE]
> A dátumok egy * biztosan változhat.
>
> Az operációsrendszer-család 5 az RDP-jelszónak legalább 10 karakterből kell lennie.
>

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum | Lejárt dátum |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.17_201803-01 |2018. április 6. |POST 5.19 |TBD |
| WA-GUEST-OS-5.16_201802-01 |2018. március 12. |POST 5.18 |TBD |
|~~WA-GUEST-OS-5.15_201801-01~~ |2018. január 4. |2018. április 6. |TBD |
|~~WA-GUEST-OS-5.14_201712-01~~ |2018. január 4. |2018. március 12. |TBD |
|~~WA-GUEST-OS-5.13_201711-01~~ |2017. december 14. |2018. január 4.|TBD |
|~~WA-GUEST-OS-5.12_201710-02~~ |2017. november 8. |2018. január 4. |TBD |
|~~WA-GUEST-OS-5.11_201709-01~~ |2017. október 6. |2017. december 14. |TBD |
|~~WA-GUEST-OS-5.10_201708-01~~ |2017. augusztus 24. |2017. december 14. |TBD |
|~~WA-GUEST-OS-5.9_201707-01~~ |2017. augusztus 3. |2017. november 8. |TBD |
|~~WA-GUEST-OS-5.8_201706-01~~ |2017. július 7. |2017. október 6. |TBD |
|~~WA-GUEST-OS-5.7_201705-01~~ |2017. június 5. |2017. augusztus 24. |TBD |
|~~WA-GUEST-OS-5.6_201704-01~~ |2017. május 9. |2017. augusztus 3. |TBD |
|~~WA-GUEST-OS-5.5_201703-01~~ |2017. április 10. |2017. július 7. |TBD |
|~~WA-GUEST-OS-5.4_201612-01~~ |2017. január 10. |2017. június 5.|TBD |

## <a name="family-4-releases"></a>Feloldja a családja 4
**Windows Server 2012 R2**

.NET-keretrendszer telepítése: 4.0-s, 4.5-ös, 4.5.1, 4.5.2.

> [!NOTE]
> A dátumok egy * biztosan változhat
>
>

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum | Lejárt dátum |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.52_201803-01 |2018. április 6. |POST 4.54 |TBD |
| WA-GUEST-OS-4.51_201802-01 |2018. március 12. |POST 4,53 |TBD |
|~~WA-GUEST-OS-4.50_201801-01~~ |2018. január 4. |2018. április 6. |TBD |
|~~WA-GUEST-OS-4.49_201712-01~~ |2018. január 4. |2018. március 12. |TBD |
|~~WA-GUEST-OS-4.48_201711-01~~ |2017. december 14. |2018. január 4. |TBD |
|~~WA-GUEST-OS-4.47_201710-02~~ |2017. november 8. |2018. január 4. |TBD |
|~~WA-GUEST-OS-4.46_201709-01~~ |2017. október 6. |2017. december 14. |TBD |
|~~WA-GUEST-OS-4.45_201708-01~~ |2017. augusztus 24. |2017. december 14. |TBD |
|~~WA-GUEST-OS-4.44_201707-01~~ |2017. augusztus 3. |2017. november 8. |TBD |
|~~WA-GUEST-OS-4.43_201706-01~~ |2017. július 7. |2017. október 6. |TBD |
|~~WA-GUEST-OS-4.42_201705-01~~ |2017. június 5. |2017. augusztus 24. |TBD |
|~~WA-GUEST-OS-4.41_201704-01~~ |2017. május 9. |2017. augusztus 3. |TBD |
|~~WA-GUEST-OS-4.40_201703-01~~ |2017. április 10. |2017. július 7. |TBD |
|~~WA-GUEST-OS-4.39_201612-01~~ |2017. január 10. |2017. június 5. |TBD |

## <a name="family-3-releases"></a>Feloldja a családja 3
**Windows Server 2012**

.NET-keretrendszer telepítése: 4.0-s, 4.5-ös, 4.5.1, 4.5.2.

> [!NOTE]
> A dátumok egy * biztosan változhat
>
>

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum | Lejárt dátum |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.59_201803-01 |2018. április 6. |POST 3.61 |TBD |
| WA-GUEST-OS-3.58_201802-01 |2018. március 19. |POST 3.60 |TBD |
|~~WA-GUEST-OS-3.57_201801-01~~ |2018. január 29. |2018. április 6. |TBD |
|~~WA-GUEST-OS-3.56_201712-01~~ |2018. január 4. |2018. március 19. |TBD |
|~~WA-GUEST-OS-3.55_201711-01~~ |2017. december 14. |2018. január 29. |TBD |
|~~WA-GUEST-OS-3.54_201710-02~~ |2017. november 8. |2018. január 4. |TBD |
|~~WA-GUEST-OS-3.53_201709-01~~ |2017. október 6. |2017. december 14. |TBD |
|~~WA-GUEST-OS-3.52_201708-01~~ |2017. augusztus 24. |2017. december 14. |TBD |
|~~WA-GUEST-OS-3.51_201707-01~~ |2017. augusztus 3. |2017. november 8. |TBD |
|~~WA-GUEST-OS-3.50_201706-01~~ |2017. július 7. |2017. október 6. |TBD |
|~~WA-GUEST-OS-3.49_201705-01~~ |2017. június 5. |2017. augusztus 24. |TBD |
|~~WA-GUEST-OS-3.48_201704-01~~ |2017. május 9. |2017. augusztus 3. |TBD |
|~~WA-GUEST-OS-3.47_201703-01~~ |2017. április 10. |2017. július 7. |TBD |
|~~WA-GUEST-OS-3.46_201612-01~~ |2017. január 10. |2017. június 5. |TBD |

## <a name="family-2-releases"></a>Feloldja a családja 2
**Windows Server 2008 R2 SP1**

.NET-keretrendszer telepítése: 3.5, 4.0-s, 4.5, 4.5.1, 4.5.2.

> [!NOTE]
> A dátumok egy * biztosan változhat
>
>

| Konfigurációs karakterlánc | Kiadási dátum | Tiltsa le a dátum | Lejárt dátum |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.72_201803-01 |2018. április 6. |POST 2.74 |TBD |
| WA-GUEST-OS-2.71_201802-01 |2018. március 12. |POST 2,73 |TBD |
|~~WA-GUEST-OS-2.70_201801-01~~ |2018. január 29. |2018. április 6. |TBD |
|~~WA-GUEST-OS-2.69_201712-01~~ |2018. január 4. |2018. március 12. |TBD |
|~~WA-GUEST-OS-2.68_201711-01~~ |2017. december 14. |2018. január 29. |TBD |
|~~WA-GUEST-OS-2.67_201710-02~~ |2017. november 8. |2018. január 4. |TBD |
|~~WA-GUEST-OS-2.66_201709-01~~ |2017. október 6. |2017. december 14. |TBD |
|~~WA-GUEST-OS-2.65_201708-01~~ |2017. augusztus 24. |2017. december 14. |TBD |
|~~WA-GUEST-OS-2.64_201707-01~~ |2017. augusztus 3. |2017. november 8. |TBD |
|~~WA-GUEST-OS-2.63_201706-01~~ |2017. július 7. |2017. október 6. |TBD |
|~~WA-GUEST-OS-2.62_201705-01~~ |2017. június 5. |2017. augusztus 24. |TBD |
|~~WA-GUEST-OS-2.61_201704-01~~ |2017. május 9. |2017. augusztus 3. |TBD |
|~~WA-GUEST-OS-2.60_201703-01~~ |2017. április 10. |2017. július 7. |TBD |
|~~WA-GUEST-OS-2.59_201701-01~~ |2017. január 10. |2017. június 5. |TBD |
|~~WA-GUEST-OS-2.58_201612-01~~ |2017. január 10. |2017. május 9.|TBD |


## <a name="msrc-patch-updates"></a>MSRC javítás frissítések
Havonta megjelenő vendég operációs rendszer részét képező javítások listáját [Itt][patches].

## <a name="sdk-support"></a>SDK-támogatás
Annak ellenére, hogy a [használatból való kivonást házirend az Azure SDK-ban] [ retire policy sdk] azt jelzi, hogy csak a fent 2.2-es verzió támogatott, a megadott Vendég operációsrendszer-családok engedélyezi, hogy korábbi verzióit. Mindig a legújabb támogatott SDK kell használnia.

| Vendég operációsrendszer-család | Kompatibilis SDK-verzió |
| --- | --- |
| 5 |Verzió 2.9.5.1+ |
| 4 |2.1-es vagy újabb |
| 3 |1.8 vagy újabb |
| 2 |1.3-as vagy újabb |
| 1 |1.0-s vagy újabb |

## <a name="guest-os-release-information"></a>Vendég operációs rendszer fontos információ
Vannak a vendég operációs rendszereinek kiadásait fontos három dátumokat: **kiadási** dátum, **le van tiltva** dátum, és **lejárati** dátum. A vendég operációs rendszer elérhető tekinthető, amikor a portálon, és a vendég operációs rendszer célként választható ki. Ha a vendég operációs rendszer elér a **le van tiltva** dátum, az Azure-ból eltávolítják azt. Bármely a vendég operációs rendszer célzó felhőalapú szolgáltatás azonban továbbra is működik normál.

Az ablak között a **le van tiltva** dátum és a **lejárati** dátum biztosít a puffer könnyebben átmeneti a egy vendég operációs rendszer egy újabb. Ha használ *automatikus* , a vendég operációs rendszer, mindig lesz a legújabb verziót, és nem kell aggódni lejár.

Ha a **lejárati** dátum továbbítja, a felhőalapú szolgáltatás továbbra is használja a vendég operációs rendszer leállítása, törlődnek, vagy frissíteni kell. További a használatból való kivonást házirenddel kapcsolatos [Itt][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Vendég operációs rendszer családja verziójú ismertetése
A Vendég operációsrendszer-családok a Microsoft Windows Server verziói alapulnak. A vendég operációs rendszer a Azure Felhőszolgáltatások futó operációs rendszer. Minden egyes vendég operációs rendszer családja, verzió, és kiadással rendelkezik számát.

* **Vendég operációsrendszer-család**  
  A Windows Server operációs rendszer kiadás, amely a vendég operációs rendszer alapul. Például *termékcsalád 3* Windows Server 2012 rendszeren alapul.
* **Vendég operációs rendszer verziója**  
  Adott termékcsalád Vendég operációsrendszer-lemezképhez, valamint a megfelelő [Microsoft biztonsági válasz Center (MSRC)] [ msrc] javítást a vendég operációs rendszer verziójának hozzák időpontban. Nem minden javítások kerülhet.

    Számok: 0-kor indulnak, és 1 növelése minden alkalommal, amikor egy új készletet frissítések kerül. Vezető nullákat csak láthatók. Ha fontos. Ez azt jelenti, hogy 2.10 verziója egy másik, sokkal újabb verziójú, mint a 2.1-es verziója.
* **Vendég operációsrendszer-kiadásnak**  
  A vendég operációs rendszer verziója kiadásában. Újbóli akkor fordul elő, ha a Microsoft problémák talál vizsgálatnál; módosítani kellene. A legújabb kiadás mindig felülír minden korábbi kiadását, nyilvános vagy sem. Az Azure-portálon csak akkor engedélyezi a felhasználók számára a legújabb kiadás egy adott verziójához. Központi telepítések korábbi kiadásáról futó rendszerint nem kényszerített, attól függően, hogy a hiba súlyossága frissítve.

Az alábbi példában 2 a családja, 12 verziója pedig "rel2" kiadása.

**Vendég operációs rendszer kiadási** - 2.12 rel2

**Ebben a kiadásban a konfigurációs karakterláncból** -WA-VENDÉG-operációsrendszer-2.12_201208-02

A konfigurációs karakterláncot a vendég operációs rendszer ezt az információt, és a dátum mely MSRC javítások vették kiadás megjelenítő beágyazott rendelkezik. Ebben a példában akár a Windows Server 2008 R2 előállított és például augusztus 2012 MSRC javítások foglalható tekintendők. Csak a kifejezetten a Windows Server adott verziójának alkalmazandó javítások tartoznak. Például ha egy MSRC javítás Microsoft Office vonatkozik, így nem is fog szerepelni, mert ennek a terméknek nem része a Windows Server alapjául szolgáló lemezképhez.

## <a name="guest-os-system-update-process"></a>Vendég operációs rendszer frissítési folyamat
Ezen a lapon a vendég operációs rendszer jövőbeli verziókkal kapcsolatos tartalmaz. Az ügyfelek jelezte, hogy azok tudni szeretné, ha kiadási oka, hogy a felhőszolgáltatás szerepköreit újraindul, ha "Automatikus" Update be lettek állítva. Vendég operációs rendszereinek kiadásait általában fordulhat elő, legalább öt (5) nap után a MSRC frissítés kiadása, amely minden hónap második keddjén jelennek meg. Új kiadásai a megfelelő MSRC javításokat minden Vendég operációsrendszer-család esetében.

A Microsoft Azure folyamatosan kiadásával frissítések. A vendég operációs rendszer csak egy ilyen frissítés folyamatban. Egy kiadási számos tényező befolyásolhatja túl sok itt listázásához. Emellett Azure szó százait több ezer gép futtatja. Ez azt jelenti, hogy nem lehetséges a pontos dátumot és időt, amikor a szerepkör(ök) újraindul. Jelenleg is dolgozunk korlátozható vagy időt vesznek egy tervet.

A vendég operációs rendszer új verziót meg van nyitva, egész Azure teljesen propagálása időbe telhet. Szolgáltatások frissítve lett, hogy az új vendég operációs rendszer, mivel azok újraindítása van frissítési tartományok érvényesítenie. "Automatikus" frissítések használandó szolgáltatások a kiadási első fogja kapni. A frissítés után az új vendég operációs rendszer verzió a szolgáltatás az Azure portálon felsorolt láthatja. Ebben az időszakban újból kiadott biztonsági frissítések fordulhat elő. Egyes verziói telepítésére hosszabb idő alatt, és az automatikus frissítési újraindítások esetleg nem kerül sor a hivatalos kiadás dátuma után sok hétig. Miután a vendég operációs rendszer nem érhető el, majd explicit módon kiválaszthatja verzió a portálról, vagy a konfigurációs fájlban.

Ezután újraindul, és további technikai adatai Vendég és a gazda operációs Rendszerével frissítések mutatók értékes információk nagy mennyiségű, lásd az MSDN blogbejegyzésében [szerepkör példány újraindítása miatt az operációs rendszer frissítései][restarts].

Ha manuálisan módosítja a vendég operációs rendszer, tekintse meg a [vendég operációs rendszer használatból való kivonást házirend] [ retirepolicy] további információt.

## <a name="guest-os-supportability-and-retirement-policy"></a>Vendég operációs rendszer támogatásának és a használatból való kivonást házirend
A vendég operációs rendszer támogatásának és a használatból való kivonást házirend kifejtett [Itt][retirepolicy].

[vendég operációs rendszer frissítési RSS-hírcsatorna]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
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
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
