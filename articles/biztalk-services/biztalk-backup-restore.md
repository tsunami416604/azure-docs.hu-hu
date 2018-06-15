---
title: Hozzon létre, és a biztonsági másolat visszaállításával a BizTalk szolgáltatások |} Microsoft Docs
description: BizTalk szolgáltatások részét képező, biztonsági mentését és helyreállítását. Megtudhatja, hogyan hozzon létre, és a biztonsági másolat visszaállításával, és határozza meg, mi lekérdezi a biztonsági mentés. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 45365092f5bcd1a8d309c10404a7437c494a8967
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102341"
---
# <a name="biztalk-services-backup-and-restore"></a>BizTalk Services: Biztonsági mentés és visszaállítás

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk-szolgáltatásokhoz kínálja biztonsági mentését és helyreállítását. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> Hibrid kapcsolatok nem készül biztonsági mentés, a kiadástól függetlenül. A hibrid kapcsolatok újra létre kell hoznia.


## <a name="before-you-begin"></a>Előkészületek
* Biztonsági mentés és visszaállítás nem lehet elérhető összes kiadása esetén. Lásd: [BizTalk szolgáltatások: kiadások diagram](biztalk-editions-feature-chart.md).
* Biztonsági mentési tartalom ugyanaz a BizTalk szolgáltatás, vagy új BizTalk szolgáltatás állíthatók vissza. A BizTalk szolgáltatás az azonos név használata visszaállításához a meglévő BizTalk szolgáltatás törölni kell, és a név elérhetőnek kell lennie. Ha töröl egy BizTalk szolgáltatás, annak elérhetőnek lennie az olyan hosszabb időt is igénybe vehet. Ha nem várja meg a néven elérhető legyen, majd állítsa vissza egy új BizTalk szolgáltatás.
* BizTalk szolgáltatások azonos kiadást vagy újabb kiadását vissza tudja állítani. BizTalk szolgáltatások visszaállítása alacsonyabb verzióra, ha a biztonsági mentés készült, nem támogatott.
  
    Például egy alapszintű kiadására biztonsági másolat visszaállítása végezhető el verziót prémium kiadásúra. A prémium kiadással biztonsági a Standard Edition nem állítható vissza.
* EDI-ellenőrző számainak folytonossága vezérlő számok készül biztonsági másolat. Üzenetek feldolgozása után utolsó biztonsági mentés, ha a biztonsági mentési tartalom visszaállítása ismétlődő vezérlő számok okozhat.
* Ha egy kötegelt aktív üzenetek, dolgozza fel a kötegelt **előtt** biztonsági másolat készítése. (A szükséges vagy ütemezett) a biztonsági másolat létrehozásakor a kötegek üzenetek soha nem kerülnek. 
  
    **Biztonsági másolatot készít aktív üzeneteket kötegekben, ha ezek az üzenetek nem készül biztonsági mentés, és ezért elvesznek.**
* Választható lehetőség: A BizTalk szolgáltatások portál leállítása felügyeleti műveleteket sem.

## <a name="create-a-backup"></a>Biztonsági mentés létrehozása
A biztonsági mentés akkor van szükség, tetszőleges időpontban, és teljesen vezérli akkor. Biztonsági mentés létrehozásához használja a [REST API-t a BizTalk szolgáltatások kezelése az Azure-on](https://msdn.microsoft.com/library/azure/dn232347.aspx).

## <a name="restore"></a>Visszaállítás
A biztonsági mentés visszaállításához használja a [REST API-t a BizTalk szolgáltatások kezelése az Azure-on](https://msdn.microsoft.com/library/azure/dn232347.aspx).

### <a name="postrestore"></a>Biztonsági másolat visszaállítása után
A BizTalk szolgáltatás mindig visszaállítása egy **felfüggesztett** állapotát. Ebben az állapotban biztosíthatja végrehajtott bármilyen konfigurációs módosításokat, mielőtt az új környezet is használható, beleértve:

* Ha létrehozta a BizTalk szolgáltatás alkalmazások az Azure BizTalk Services SDK használatával, esetleg az Access Control (ACS) hitelesítő adatait a ezeknek az alkalmazásoknak a visszaállított környezet használata.
* A BizTalk szolgáltatás replikálni egy meglévő BizTalk Service-környezet visszaállítása Ebben a helyzetben FTP forrásmappa, használja az eredeti BizTalk szolgáltatások portálon konfigurált szerződések esetén szükség lehet frissíteni a megállapodások az újonnan visszaállított környezetben egy másik FTP forrásmappa használatára. Ellenkező esetben lehet két különböző megállapodások ugyanaz az üzenet lekérésére tett kísérlet.
* Ha szeretné, hogy több BizTalk szolgáltatás környezet visszaállítása, ellenőrizze, hogy célozhat meg a megfelelő környezet a Visual Studio alkalmazást, a PowerShell-parancsmagok, a REST API-k vagy a kereskedelmi Partner OM API-val.
* Ajánlott az automatikus biztonsági mentések konfigurálása az újonnan visszaállított BizTalk szolgáltatás környezetben.

## <a name="what-gets-backed-up"></a>Mi a biztonsági mentés beolvasása
Biztonsági másolat létrehozásakor a következő elemek biztonsági mentése:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Biztonsági mentésben szereplő elemek</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Az Azure BizTalk szolgáltatások portálja</strong></td>
</tr> 
<tr>
<td>Konfigurációs és futásidejű</td> 
<td>
<ul>
<li>Partnerek és a profil részletei</li>
<li>Egyezmények</li>
<li>Egyéni szerelvényeket telepített</li>
<li>Telepített hidak</li>
<li>Tanúsítványok</li>
<li>Telepített átalakítások</li>
<li>Folyamatok</li>
<li>Sablonok létrehozása és mentése a BizTalk szolgáltatások portálon</li>
<li>X12 ST01 és GS01</li>
<li>Vezérlő számok (EDI)</li>
<li>AS2-üzenet MIC értékek</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Azure BizTalk szolgáltatás</strong></td>
</tr> 
<tr>
<td>SSL-tanúsítvány</td> 
<td>
<ul>
<li>SSL-Tanúsítványadatok</li>
<li>SSL-tanúsítvány jelszava</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk szolgáltatás beállításai</td> 
<td>
<ul>
<li>Méretezési egységek száma</li>
<li>Kiadás</li>
<li>Termékverzió</li>
<li>Régió/adatközpont</li>
<li>Access Control Service (ACS) névtér és a kulcs</li>
<li>Adatbázis-kapcsolati karakterlánc nyomon követése</li>
<li>Archiválási tárolási fiók kapcsolati karakterlánc</li>
<li>Figyelési tárolási fiók kapcsolati karakterlánc</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>További elemek</strong></td>
</tr> 
<tr>
<td>Adatbázis nyomon követése</td> 
<td>A BizTalk szolgáltatás létrehozása, ha a nyomkövetési adatainak van-e megadva, beleértve az Azure SQL adatbázis-kiszolgáló és a nyomkövetési adatbázisnevet. A követés adatbázis nem automatikusan biztonsági másolat.
<br/><br/>
<strong>Fontos</strong><br/>
Ha a nyomkövetési adatbázisa törlődik, és az adatbázisban kell helyreállítani, a korábbi biztonsági léteznie kell. Ha a biztonsági mentés nem létezik, a követési adatbázis és az adatokat is nem helyreállítható. Ebben a helyzetben új követési adatbázis létrehozása a azonos nevű adatbázis. A Georeplikáció ajánlott.</td>
</tr> 
</table>

## <a name="next"></a>Következő lépés
Azure BizTalk szolgáltatások létrehozásához lépjen a [BizTalk szolgáltatások: kiépítés](http://go.microsoft.com/fwlink/p/?LinkID=302280). Az alkalmazások létrehozásának megkezdéséhez ugorjon az [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197) című témakörre.

## <a name="see-also"></a>Lásd még:
* [Biztonsági mentési BizTalk szolgáltatás](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Állítsa vissza biztonsági másolatból BizTalk szolgáltatás](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk szolgáltatások: Fejlesztői, Basic, Standard és prémium kiadás diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk szolgáltatások: kiépítés](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Kiépítési állapot diagramja](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Irányítópult, Figyelés és Méret lapok](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Szabályozás](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Kiállító neve és kiállító kulcsa](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Hogyan kezdhetem el az Azure BizTalk Services SDK használatát](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

