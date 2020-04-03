---
title: További információ az Azure vendégoperációs rendszerének legújabb kiadásairól | Microsoft dokumentumok
description: A legújabb kiadási hírek és SDK-kompatibilitás az Azure Cloud Services vendég operációs rendszeréhez.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 4/2/2020
ms.author: raiye
ms.openlocfilehash: 95c27cd906717d52a232b5ed85eba2b93bc6569d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618293"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Az Azure vendég operációs rendszer kiadásai és az SDK-kompatibilitási mátrix
Naprakész információkat tartalmaz a felhőszolgáltatásokhoz való legújabb Azure Vendég operációsrendszer-kiadásokról. Ez az információ segít megtervezni a frissítési útvonalat, mielőtt a vendég operációs rendszer le van tiltva. Ha úgy konfigurálja a szerepköröket, hogy az [Azure Vendég operációsrendszer-frissítési beállításaiban][Azure Guest OS Update Settings]leírtak szerint *automatikus* vendég operációsrendszer-frissítéseket használjanak, nem létfontosságú, hogy elolvassa ezt a lapot.

> [!IMPORTANT]
> Ez a lap a Felhőszolgáltatások webes és feldolgozói szerepkörökre vonatkozik, amelyek egy vendég operációs rendszeren futnak. Nem vonatkozik az IaaS virtuális **gépekre.**
>
>


> [!TIP]
>  Iratkozzon fel a [vendég operációs rendszer rss-hírcsatorna frissítése,] hogy megkapja a legtöbb időben értesítést az összes vendég operációs rendszer változások.
>
>

> [!IMPORTANT]
> Csak a vendég operációs rendszer legújabb 2 verziója lesz támogatott és elérhető az Azure Portalon.
>
>

Nem biztos abban, hogyan lehet frissíteni a vendég operációs rendszer? [Ezt][cloud updates] nézd meg.

## <a name="news-updates"></a>Hírek

###### <a name="april-2-2020"></a>**2020. április 2.**
A márciusvendég operációs rendszer megjelent. 

###### <a name="march-5-2020"></a>**2020. március 5.**
A februári vendég operációs rendszer megjelent. 

###### <a name="january-24-2020"></a>**2020. január 25.**
A januári vendég operációs rendszer megjelent. 

###### <a name="january-8-2020"></a>**2020. január 8.**
A decemberi vendég operációs rendszer megjelent.

###### <a name="december-5-2019"></a>**2019. december 5.,**
A november vendég operációs rendszer megjelent.

###### <a name="november-1-2019"></a>**2019. november 1.**
Az októberi vendég operációs rendszer megjelent.

###### <a name="october-7-2019"></a>**2019. október 7.October 7, 2019**
A szeptember vendég operációs rendszer megjelent.

###### <a name="september-4-2019"></a>**2019. szeptember 4.September 4, 2019**
Az augusztusi vendég operációs rendszer megjelent.

###### <a name="july-26-2019"></a>**2019. július 26.July 26, 2019**
A július vendég operációs rendszer megjelent.

###### <a name="july-8-2019"></a>**2019. július 8.July 8, 2019**
A júniusi vendég operációs rendszer megjelent.

###### <a name="june-6-2019"></a>**2019. június 6.June 6, 2019**
A május vendég os birtokol mentesít.

###### <a name="may-7-2019"></a>**2019. május 7.May 7, 2019**
Az áprilisi vendég operációs rendszer megjelent.

###### <a name="march-26-2019"></a>**2019. március 26.March 26, 2019**
A márciusvendég operációs rendszer megjelent.

###### <a name="march-12-2019"></a>**2019. március 12.March 12, 2019**
A februári vendég operációs rendszer megjelent.

###### <a name="february-5-2019"></a>**2019. február 5.,**
A januári vendég operációs rendszer megjelent.

###### <a name="january-24-2019"></a>**2019. január 24., 2019. január 24.**
A Family 6 Guest OS (Windows Server 2019) kioldott.

###### <a name="january-7-2019"></a>**2019. január 7.,**
A decemberi vendég operációs rendszer megjelent.

###### <a name="december-14-2018"></a>**2018. december 14.December 14.December 14.December 14., 2018**
A november vendég operációs rendszer megjelent.

###### <a name="november-8-2018"></a>**2018. november 8.**
Az októberi vendég operációs rendszer megjelent.

###### <a name="october-12-2018"></a>**2018. október 12.October 12, 2018**
A szeptember vendég operációs rendszer megjelent.

## <a name="releases"></a>Kiadások

## <a name="family-6-releases"></a>Family 6 kiadások
**Windows Server 2019**

.NET Framework telepítve: 3.5, 4.7.2

> [!NOTE]
> A Windows Azure SDK for .NET - 3.0 [letölthető itt][Windows Azure SDK].
>
>Telepítési lépések:
>1. Távolítsa el a MicrosoftAzureAuthoringTools*.msi régebbi verzióit
>2. Telepítse az [Azure SDK-t a .NET - 3.0-hoz][Windows Azure SDK]
>3. A számítógép újraindítása
>4. Hozzon létre egy új Cloud Service-projektet, és adjon hozzá egyetlen feldolgozói szerepkört
>5. Az operációs rendszer családjának módosítása 6-ra és csomag létrehozása
>6. A csomag üzembe helyezése az Azure-ban az Azure Portalon vagy a Visual Studióban
>
>Vendég OS Family 6 kiadás kényszeríti TLS 1.2 explicit letiltásával TLS 1.0 és 1.1, és meghatározza egy adott készlet rejtjelező csomagok. További [more]információ.


| Konfigurációs karakterlánc | Kiadási dátum | Dátum letiltása |
| --- | --- | --- |
|  WA-GUEST-OS-6.17_202003-01  |  2020. április 2.  |  Hozzászólás 6,19  |  
|  WA-GUEST-OS-6.16_202002-01  |  2020. március 5.  |  Oszlop 6,18  |  
|~~WA-GUEST-OS-6.15_202001-01~~|  2020. január 25.  |  2020. április 2.  |  
|~~WA-GUEST-OS-6.14_201912-01~~| 2020. január 8. | 2020. március 5. |  
|~~WA-GUEST-OS-6.13_201911-01~~| 2019. december 5., | 2020. január 25. |  
|~~WA-GUEST-OS-6.12_201910-01~~| 2019. november 1. | 2020. január 8. |  
|~~WA-GUEST-OS-6.11_201909-01~~| 2019. október 7.October 7, 2019 | 2019. december 5., |  
|~~WA-GUEST-OS-6.10_201908-01~~| 2019. augusztus 4.August 4, 2019 | 2019. november 1.  |  
|~~WA-GUEST-OS-6.9_201907-0~~|2019. július 26.July 26, 2019 | 2019. október 7.October 7, 2019 |
|~~WA-GUEST-OS-6.8_201906-01~~|2019. július 8.July 8, 2019 |2019. augusztus 4.August 4, 2019 |
|~~WA-GUEST-OS-6.7_201905-01~~ |2019. június 6.June 6, 2019 |2019. július 26.July 26, 2019 |
|~~WA-GUEST-OS-6.6_201904-01~~ |2019. május 7.May 7, 2019 |2019. július 8.July 8, 2019 |
|~~WA-GUEST-OS-6.5_201903-01~~ |2019. március 26.March 26, 2019 |2019. június 6.June 6, 2019 |
|~~WA-GUEST-OS-6.4_201902-01~~ |2019. március 12.March 12, 2019 |2019. május 7.May 7, 2019 |
|~~WA-GUEST-OS-6.3_201901-01~~ |2019. február 5., |2019. március 26.March 26, 2019 |
|~~WA-GUEST-OS-6.2_201812-01~~ |2019. január 24., 2019. január 24. |2019. március 12.March 12, 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |2019. január 24., 2019. január 24. |2019. február 5., |

## <a name="family-5-releases"></a>Family 5 kiadások
**Windows Server 2016**

.NET Framework telepítve: 3.5, 4.6

> [!NOTE]
> Az 5-ös operációsrendszer-család RDP-jelszavának legalább 10 karakternek kell lennie.
>


| Konfigurációs karakterlánc | Kiadási dátum | Dátum letiltása |
| --- | --- | --- |
|  WA-GUEST-OS-5.41_202003-01  |  2020. április 2.  |  Hozzászólás 5,43  |  
|  WA-GUEST-OS-5.40_202002-01  |  2020. március 5.  |  Hozzászólás 5,42  |  
|~~WA-GUEST-OS-5.39_202001-01~~|  2020. január 25.  |  2020. április 2.  |  
|~~WA-GUEST-OS-5.38_201912-01~~| 2020. január 8. | 2020. március 5. |  
|~~WA-GUEST-OS-5.37_201911-01~~| 2019. december 5., | 2020. január 25. |  
|~~WA-GUEST-OS-5.36_201910-01~~| 2019. november 1. | 2020. január 8. |  
|~~WA-GUEST-OS-5.35_201909-01~~| 2019. október 7.October 7, 2019 | 2019. december 5., |  
|~~WA-GUEST-OS-5.34_201908-01~~|  2019. augusztus 4.August 4, 2019  | 2019. november 1. |  
|~~WA-GUEST-OS-5.33_201907-01~~| 2019. július 26.July 26, 2019 | 2019. október 7.October 7, 2019 |  
|~~WA-GUEST-OS-5.32_201906-01~~|2019. július 8.July 8, 2019 |2019. augusztus 4.August 4, 2019 |
|~~WA-GUEST-OS-5.31_201905-01~~ |2019. június 6.June 6, 2019 |2019. július 26.July 26, 2019 |
|~~WA-GUEST-OS-5.30_201904-01~~ |2019. május 7.May 7, 2019 |2019. július 8.July 8, 2019 |
|~~WA-GUEST-OS-5.29_201903-01~~ |2019. március 26.March 26, 2019 |2019. június 6.June 6, 2019 |
|~~WA-GUEST-OS-5.28_201902-01~~ |2019. március 12.March 12, 2019 |2019. május 7.May 7, 2019 |
|~~WA-GUEST-OS-5.27_201901-01~~ |2019. február 5., |2019. március 26.March 26, 2019 |
|~~WA-GUEST-OS-5.26_201812-01~~ |2019. január 7., |2019. március 12.March 12, 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |2018. december 14.December 14.December 14.December 14., 2018 |2019. február 5., |
|~~WA-GUEST-OS-5.24_201810-01~~ |2018. november 8. |2019. január 7., |
|~~WA-GUEST-OS-5.23_201809-01~~ |2018. október 12.October 12, 2018 |2018. december 14.December 14.December 14.December 14., 2018 |

## <a name="family-4-releases"></a>Family 4 kiadások
**Windows Server 2012 R2**

.NET Framework telepítve: 3.5, 4.5.1, 4.5.2

| Konfigurációs karakterlánc | Kiadási dátum | Dátum letiltása |
| --- | --- | --- |
|  WA-GUEST-OS-4.76_202003-01  |  2020. április 2.  |  Post 4,78  |  
|  WA-GUEST-OS-4.75_202002-01  |  2020. március 5.  |  Post 4,77  |  
|~~WA-GUEST-OS-4.74_202001-01~~|  2020. január 25.  |  2020. április 2.  |  
|~~WA-GUEST-OS-4.73_201912-01~~| 2020. január 8. | 2020. március 5. |  
|~~WA-GUEST-OS-4.72_201911-01~~| 2019. december 5., | 2020. január 25. |  
|~~WA-GUEST-OS-4.71_201910-01~~| 2019. november 1. | 2020. január 8. |  
|~~WA-GUEST-OS-4.70_201909-01~~| 2019. október 7.October 7, 2019 | 2019. december 5., |  
|~~WA-GUEST-OS-4.69_201908-01~~| 2019. augusztus 4.August 4, 2019 | 2019. november 1. |  
|~~WA-GUEST-OS-4.68_201907-01~~| 2019. július 26.July 26, 2019  | 2019. október 7.October 7, 2019 |
|~~WA-GUEST-OS-4.67_201906-01~~| 2019. július 8.July 8, 2019 |2019. augusztus 4.August 4, 2019 |
|~~WA-GUEST-OS-4.66_201905-01~~ |2019. június 6.June 6, 2019 |2019. július 26.July 26, 2019 |
|~~WA-GUEST-OS-4.65_201904-01~~ |2019. május 7.May 7, 2019 |2019. július 8.July 8, 2019 |
|~~WA-GUEST-OS-4.64_201903-01~~ |2019. március 26.March 26, 2019 |2019. június 6.June 6, 2019 |
|~~WA-GUEST-OS-4.63_201902-01~~ |2019. március 12.March 12, 2019 |2019. május 7.May 7, 2019 |
|~~WA-GUEST-OS-4.62_201901-01~~ |2019. február 5., |2019. március 26.March 26, 2019 |
|~~WA-GUEST-OS-4.61_201812-01~~ |2019. január 7., |2019. március 12.March 12, 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |2018. december 14.December 14.December 14.December 14., 2018 |2019. február 5., |
|~~WA-GUEST-OS-4.59_201810-01~~ |2018. november 8. |2019. január 7., |
|~~WA-GUEST-OS-4.58_201809-01~~ |2018. október 12.October 12, 2018 |2018. december 14.December 14.December 14.December 14., 2018 |

## <a name="family-3-releases"></a>Family 3 kiadások
**Windows Server 2012**

.NET Framework telepítve: 3.5, 4.5

| Konfigurációs karakterlánc | Kiadási dátum | Dátum letiltása |
| --- | --- | --- |
|  WA-GUEST-OS-3.83_202003-01  |  2020. április 2.  |  Poszt 3,85  |  
|  WA-GUEST-OS-3.82_202002-01  |  2020. március 5.  |  Poszt 3,84  |  
|~~WA-GUEST-OS-3.81_202001-01~~|  2020. január 25.  |  2020. április 2.  |  
|~~WA-GUEST-OS-3.80_201912-01~~| 2020. január 8. | 2020. március 5. |  
|~~WA-GUEST-OS-3.79_201911-01~~| 2019. december 5., | 2020. január 25. |  
|~~WA-GUEST-OS-3.78_201910-01~~| 2019. november 1. | 2020. január 8. |  
|~~WA-GUEST-OS-3.77_201909-01~~| 2019. október 7.October 7, 2019 | 2019. december 5., |  
|~~WA-GUEST-OS-3.76_201908-01~~|  2019. augusztus 4.August 4, 2019  |  2019. november 1.  |  
|~~WA-GUEST-OS-3.75_201907-01~~| 2019. július 26.July 26, 2019 | 2019. október 7.October 7, 2019 |
|~~WA-GUEST-OS-3.74_201906-01~~| 2019. július 8.July 8, 2019 |2019. augusztus 4.August 4, 2019 |
|~~WA-GUEST-OS-3.73_201905-01~~ |2019. június 6.June 6, 2019 |2019. július 26.July 26, 2019 |
|~~WA-GUEST-OS-3.72_201904-01~~ |2019. május 7.May 7, 2019 |2019. július 8.July 8, 2019 |
|~~WA-GUEST-OS-3.71_201903-01~~ |2019. március 26.March 26, 2019 |2019. június 6.June 6, 2019 |
|~~WA-GUEST-OS-3.70_201902-01~~ |2019. március 12.March 12, 2019 |2019. május 7.May 7, 2019 |
|~~WA-GUEST-OS-3.69_201901-01~~ |2019. február 5., |2019. március 26.March 26, 2019 |
|~~WA-GUEST-OS-3.68_201812-01~~ |2019. január 7., |2019. március 12.March 12, 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |2018. december 14.December 14.December 14.December 14., 2018 |2019. február 5., |
|~~WA-GUEST-OS-3.66_201810-01~~ |2018. november 8. |2019. január 7., |
|~~WA-GUEST-OS-3.65_201809-01~~ |2018. október 12.October 12, 2018 |2018. december 14.December 14.December 14.December 14., 2018 |

## <a name="family-2-releases"></a>Family 2 kiadások
**Windows Server 2008 R2 SP1**

.NET Framework telepítve: 3.5 (tartalmazza a 2.0 és 3.0), 4.5

| Konfigurációs karakterlánc | Kiadási dátum | Dátum letiltása |
| --- | --- | --- |
|  WA-GUEST-OS-2.96_202003-01  |  2020. április 2.  |  Poszt 2,98  |  
|  WA-GUEST-OS-2.95_202002-01  |  2020. március 5.  |  Poszt 2,97  |  
|~~WA-GUEST-OS-2.94_202001-01~~|  2020. január 25.  |  2020. április 2.  |  
|~~WA-GUEST-OS-2.93_201912-01~~| 2020. január 8. | 2020. március 5. |  
|~~WA-GUEST-OS-2.92_201911-01~~| 2019. december 5., | 2020. január 25. |  
|~~WA-GUEST-OS-2.91_201910-01~~| 2019. november 1. | 2020. január 8. |  
|~~WA-GUEST-OS-2.90_201909-01~~| 2019. október 7.October 7, 2019 | 2019. december 5., |  
|~~WA-GUEST-OS-2.89_201908-01~~| 2019. augusztus 4.August 4, 2019 | 2019. november 1. |  
|~~WA-GUEST-OS-2.88_201907-01~~| 2019. július 26.July 26, 2019 | 2019. október 7.October 7, 2019 |
|~~WA-GUEST-OS-2.87_201906-01~~|2019. július 8.July 8, 2019 | 2019. augusztus 4.August 4, 2019 |
|~~WA-GUEST-OS-2.86_201905-01~~ |2019. június 6.June 6, 2019 |2019. július 26.July 26, 2019 |
|~~WA-GUEST-OS-2.85_201904-01~~ |2019. május 7.May 7, 2019 |2019. július 8.July 8, 2019 |
|~~WA-GUEST-OS-2.84_201903-01~~ |2019. március 26.March 26, 2019 |2019. június 6.June 6, 2019 |
|~~WA-GUEST-OS-2.83_201902-01~~ |2019. március 12.March 12, 2019 |2019. május 7.May 7, 2019 |
|~~WA-GUEST-OS-2.82_201901-01~~ |2019. február 5., |2019. március 26.March 26, 2019 |
|~~WA-GUEST-OS-2.81_201812-01~~ |2019. január 7., |2019. március 12.March 12, 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |2018. december 14.December 14.December 14.December 14., 2018 |2019. február 5., |
|~~WA-GUEST-OS-2.79_201810-01~~ |2018. november 8. |2019. január 7., |
|~~WA-GUEST-OS-2.78_201809-01~~ |2018. október 12.October 12, 2018 |2018. december 14.December 14.December 14.December 14., 2018 |

## <a name="msrc-patch-updates"></a>MSRC-javítás frissítései
Az egyes havi Vendég operációs rendszer kiadásaihoz mellékelt javítások listája [itt][patches]érhető el.

## <a name="sdk-support"></a>SDK-támogatás
Annak ellenére, hogy [az Azure SDK levonási szabályzatazt][retire policy sdk] jelzi, hogy csak a 2.2 feletti verziók támogatottak, adott vendég operációsrendszer-családok lehetővé teszik a korábbi verziók használatát. Mindig a legújabb támogatott SDK-t használja.

| Vendég operációsrendszer-család | Kompatibilis SDK-verziók |
| --- | --- |
| 6 |2.9.6+ verzió |
| 5 |2.9.5.1+ verzió |
| 4 |2.1+-os verzió |
| 3 |1.8+-os verzió |
| 2 |1.3-as verzió+ |
| 1 |1.0+-os verzió |

## <a name="guest-os-release-information"></a>Vendég operációs rendszer kiadási információk
Három dátum, amelyek fontosak a vendég operációs rendszer kiadások: **kiadás** dátuma, **letiltott** dátum és **lejárati** dátum. A vendég operációs rendszer akkor tekinthető elérhetőnek, ha a portálon, és ki választható a cél vendég operációs rendszer. Amikor egy vendég operációs rendszer eléri a **letiltott** dátumot, eltávolítja az Azure-ból. Azonban a vendég operációs rendszert célzó felhőszolgáltatás továbbra is a szokásos módon fog működni.

A **letiltott** dátum és a **lejárati** dátum közötti ablak puffert biztosít az egyik vendég operációs rendszerről egy újabbra való egyszerű áttéréshez. Ha *az automatikus,* mint a vendég operációs rendszer, akkor mindig a legújabb verziót, és nem kell aggódnia, hogy lejár.

A **lejárati** dátum lejártakor a vendég operációs rendszert továbbra is használó felhőszolgáltatások leállnak, törlődnek vagy frissítésre kényszerülnek. A nyugdíjpolitikáról [itt][retirepolicy]olvashat bővebben.

## <a name="guest-os-family-version-explanation"></a>Vendég operációs rendszer család-verzió magyarázata
A vendég operációsrendszer-családok a Microsoft Windows Server kiadott verzióin alapulnak. A vendég operációs rendszer az alapul szolgáló operációs rendszer, amelyen az Azure Cloud Services fut. Minden vendég operációs rendszer rendelkezik egy család, verzió és kiadási szám.

* **Vendég operációsrendszer-család**  
  Olyan Windows Server operációs rendszer, amelyen a vendég operációs rendszer alapul. A *3-as család* például a Windows Server 2012 rendszeren alapul.
* **Vendég operációs rendszer verziója**  
  A vendég operációsrendszer-család lemezképére és a [microsoft security response center (MSRC)][msrc] vonatkozó javításaira jellemző, amelyek az új vendég operációsrendszer-verzió előállításának napján érhetők el. Előfordulhat, hogy nem minden tapasz ttartalmazza.

    A számok 0-nál kezdődnek, és 1-nél növeksznek minden alkalommal, amikor új frissítéseket adnak hozzá. A záró nullák csak akkor jelennek meg, ha fontosak. Ez azt, hogy a 2.10-es verzió egy másik, sokkal későbbi verzió, mint a 2.1-es verzió.
* **Vendég operációs rendszer kiadása**  
  A vendég operációs rendszer verziójának újbóli kiadása. Újrakiadás történik, ha a Microsoft problémákat talál a tesztelés során; módosításokat igényel. A legújabb kiadás mindig felülírja a korábbi kiadások, nyilvános vagy sem. Az Azure Portal csak lehetővé teszi a felhasználók számára, hogy válassza ki a legújabb kiadás egy adott verzióhoz. Az előző kiadáson futó központi telepítések általában nem vannak a hiba súlyosságától függően nem történnek a frissítés kényszerítése.

Az alábbi példában a 2 a család, a 12 a verzió és a "rel2" a kiadás.

**Vendég operációs rendszer kiadás** - 2,12 rel2

**Konfigurációs karakterlánc ehhez a kiadáshoz** - WA-GUEST-OS-2.12_201208-02

A vendég operációs rendszer konfigurációs karakterlánca ugyanazt az információt tartalmazza, valamint egy dátumot, amely megmutatja, hogy mely MSRC-javításokat tekintették az adott kiadáshoz. Ebben a példában a Windows Server 2008 R2 rendszerhez 2012 augusztusáig gyártott MSRC-javításokat vették figyelembe. Csak a Windows Server adott verziójára kifejezetten érvényes javítások szerepelnek. Ha például egy MSRC-javítás a Microsoft Office programra vonatkozik, az nem jelenik meg, mert az nem része a Windows Server alaplemezképének.

## <a name="guest-os-system-update-process"></a>Vendég operációs rendszer rendszerfrissítési folyamata
Ez az oldal információkat tartalmaz a közelgő vendég operációs rendszer releases. Az ügyfelek jelezték, hogy szeretnék tudni, hogy mikor történik egy kiadás, mert a felhőalapú szolgáltatás szerepkörök újraindulnak, ha "Automatikus" frissítésre vannak beállítva. A vendég operációs rendszer kiadásai általában 2-3 héttel az MSRC minden hónap második keddjén történő frissítésután következnek be. Az új kiadások tartalmazzák az egyes vendég operációsrendszer-családokhoz tartozó összes releváns MSRC-javítást.

A Microsoft Azure folyamatosan frissíti a frissítéseket. A vendég operációs rendszer csak egy ilyen frissítés a folyamatban. A kiadást számos tényező befolyásolhatja, amelyek túl sok a listázáshoz. Emellett az Azure szó szerint több százezer gépen fut. Ez azt jelenti, hogy lehetetlen pontos dátumot és időpontot adni, amikor a szerepkör(ek) újraindulnak. Dolgozunk egy tervet, hogy korlátozzák vagy idő újraindítása.

A vendég operációs rendszer új kiadásának közzétételekor időbe telhet az Azure-ban való teljes körű propagálás. Aszolgáltatások frissítése az új vendég operációs rendszerre, újraindítja őket a frissítési tartományok tiszteletben Az "Automatikus" frissítések használatára beállított szolgáltatások először kiadást kapnak. A frissítés után megjelenik az új vendég operációs rendszer verziója a szolgáltatás az Azure Portalon. Ebben az időszakban újra kiadások léphetnek fel. Egyes verziók hosszabb ideig telepíthetők, és előfordulhat, hogy az automatikus frissítés-újraindítás oka a hivatalos megjelenési dátum után hetekig nem fordul elő. Miután egy vendég operációs rendszer elérhetővé válik, explicit módon kiválaszthatja az adott verziót a portálról vagy a konfigurációs fájlból.

Az újraindításokkal kapcsolatos értékes információkért és a vendég- és gazdaoperációsrendszer-frissítések technikai részleteire mutató további információkért tekintse meg az MSDN [szerepkörpéldány újraindítása az operációs rendszer frissítése idefjei miatt című blogbejegyzést.][restarts]

Ha manuálisan frissíti a vendég operációs rendszert, további információkért tekintse meg a [vendég operációs rendszer levonási szabályzatát.][retirepolicy]

## <a name="guest-os-supportability-and-retirement-policy"></a>Vendég operációs rendszer támogathatósága és a nyugdíjazási szabályzat
A vendég operációs rendszer támogathatósága és a nyugdíjazási politika [itt][retirepolicy]ismertethető.

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Vendég operációsrendszer-frissítés RSS-hírcsatornája]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[Több]: https://docs.microsoft.com/azure/cloud-services/applications-dont-support-tls-1-2  
