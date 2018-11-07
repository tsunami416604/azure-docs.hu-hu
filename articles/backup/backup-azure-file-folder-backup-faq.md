---
title: Az Azure Backup ügynöke – GYIK
description: 'Válaszok a gyakori kérdésekre a következőkkel kapcsolatban: az Azure Backup-ügynök működése, biztonsági mentési és megőrzési korlátok.'
services: backup
author: trinadhk
manager: shreeshd
keywords: biztonsági mentés és vészhelyreállítás; biztonsági mentési szolgáltatás
ms.service: backup
ms.topic: conceptual
ms.date: 8/6/2018
ms.author: saurse;trinadhk
ms.openlocfilehash: cf27848b6ca63f06c7d452ac67908cb85dbafd47
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239839"
---
# <a name="questions-about-the-azure-backup-agent"></a>Kérdések az Azure Backup-ügynökkel kapcsolatban
A cikk gyakori kérdésekre adott válaszokat tartalmazó szakaszaiban gyorsan áttekinthető az Azure Backup-ügynök összetevőinek működése. Egyes válaszokban részletes információkat tartalmazó cikkekre mutató hivatkozások találhatók. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása
### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>Honnan tölthetem le a legújabb Azure Backup ügynököt? <br/>
[Innen](https://aka.ms/azurebackup_agent) töltheti le a legújabb ügynököt a Windows Server, System Center DPM vagy Windows-ügyfél biztonsági mentése számára. Ha egy virtuális gépről szeretne biztonsági mentést készíteni, használja a virtuálisgép-ügynököt (amely automatikusan telepíti a megfelelő bővítményt). Az Azure katalógusból létrehozott virtuális gépeken a virtuálisgép-ügynök már jelen van.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Az Azure Backup ügynök telepítése közben a rendszer a tároló hitelesítő adatainak megadását kéri. A tároló hitelesítő adatai lejárnak?
Igen, a tároló hitelesítő adatai 48 óra után lejárnak. Ha a fájl lejár, jelentkezzen be az Azure Portalon, és töltse le a tároló hitelesítő adatfájljait a tárolóból.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>Milyen típusú meghajtókon lévő fájlokról és mappákról tudok biztonsági másolatot készíteni? <br/>
A következő meghajtókról/kötetekről nem készíthető biztonsági másolat:

* Cserélhető adathordozók: a biztonsági mentés minden forráselemének rögzített állapotban kell lennie.
* Csak olvasható kötetek: A kötetnek a működéshez írhatónak kell lennie a kötet árnyékmásolat (VSS) számára.
* Offline kötetek: A kötetnek a működéshez online-nak kell lennie a VSS számára.
* Hálózati megosztás: A kötetnek helyinek kell lennie a kiszolgáló számára, hogy az online biztonsági mentés használatával biztonsági másolatot lehessen róla készíteni.
* BitLocker által védett kötetek: A kötetet fel kell oldani a biztonsági mentés megkezdése előtt.
* Fájlrendszer azonosítása: Csak az NTFS fájlrendszer támogatott.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>A kiszolgálón lévő milyen fájlokról és mappákról készíthetek biztonsági másolatot?<br/>
A következő típusok támogatottak:

* Titkosított
* Tömörített
* Ritka
* Tömörített + ritka
* Rögzített hivatkozások: Nem támogatott, átugorva
* Újraelemzési pont: Nem támogatott, átugorva
* Titkosított + ritka: Nem támogatott, átugorva
* Tömörített stream: Nem támogatott, átugorva
* Ritka stream: Nem támogatott, átugorva

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>Telepíthetem az Azure Backup ügynököt egy olyan Azure virtuális gépre, amelyet az Azure Backup szolgáltatás már támogat a virtuális gép bővítmény használatával? <br/>
Abszolút. Az Azure Backup virtuálisgép-szintű biztonsági mentést nyújt az Azure virtuális gépeknek az erre szolgáló bővítmény használatával. A vendég Windows operációs rendszeren található fájlok és mappák védelme érdekében telepítse az Azure Backup ügynököt a vendég Windows operációs rendszeren.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Telepíthetem az Azure Backup ügynököt egy Azure virtuális gépre, hogy biztonsági mentést készítsek egy, az Azure virtuális gép által biztosított ideiglenes tárolón lévő fájlokról és mappákról? <br/>
Igen. Telepítse az Azure Backup ügynököt a vendég Windows operációs rendszeren, és készítsen biztonsági mentést a fájlokról és mappákról egy ideiglenes tárolóra. A biztonsági mentési feladatok sikertelenek lesznek, ha törli az ideiglenes tároló adatait. Emellett ha az ideiglenes tároló adatait törölték, csak állandó tárolóba tud visszaállítani.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>Mi a gyorsítótármappa minimális méretkövetelménye? <br/>
A gyorsítótármappa mérete határozza meg azt az adatmennyiséget, amelyről biztonsági másolatot készít. Az ügyfélgyorsítótár mappája a kötet legyen legalább 5 – 10 % szabad terület, a biztonsági mentési adatok teljes mérete képest. Ha a kötet kisebb, mint 5 % szabad terület van, vagy növelje a kötet méretét, vagy [helyezze át a gyorsítótármappa egy elegendő szabad lemezterülettel rendelkező kötetre](backup-azure-file-folder-backup-faq.md#backup).

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>Hogyan regisztrálhatom a kiszolgálómat egy másik adatközpontra?<br/>
A biztonsági mentési adatokat a rendszer annak a tárolónak az adatközpontjába küldi, amelyikre regisztrálva van. Az adatközpont módosításának legegyszerűbb módja az ügynök eltávolítása, újratelepítése, majd a kívánt adatközponthoz tartozó új tárolóra regisztrálása.

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Az Azure Backup ügynök működik egy Windows Server 2012 deduplikációt használó kiszolgálón? <br/>
Igen. Az ügynökszolgáltatás átalakítja a deduplikált adatokat normális adatokká, amikor előkészíti a biztonsági mentési műveletet. Ezután optimalizálja az adatokat a biztonsági mentéshez, titkosítja őket, majd elküldi a titkosított adagokat az online biztonsági mentési szolgáltatásba.

## <a name="prerequisites-and-dependencies"></a>Előfeltételek és a függőségek
### <a name="what-features-of-microsoft-azure-recovery-services-mars-agent-require-net-framework-452-and-higher"></a>Milyen funkciók a Microsoft Azure Recovery Services-(MARS) ügynök szükséges a .NET-keretrendszer 4.5.2-es vagy újabb?
A [azonnali visszaállítása](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) funkció, amely lehetővé teszi, hogy az egyes fájlok és mappák visszaállítására *adatok helyreállítása* varázsló megköveteli a .NET-keretrendszer 4.5.2-es vagy újabb verziója.

## <a name="backup"></a>Backup
### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Hogyan változtathatom meg az Azure Backup ügynök számára meghatározott gyorsítótár helyét?<br/>
Használja az alábbi listát a gyorsítótár helyének módosításához.

1. Állítsa le a Backup motort úgy, hogy végrehajtja a következő parancsot egy rendszergazdai jogú parancssorban:

    ```PS C:\> Net stop obengine``` 
  
2. Ne helyezze át a fájlokat. Ehelyett másolja a gyorsítótár mappáját egy másik, elegendő tárhellyel rendelkező meghajtóra. Miután ellenőrizte, hogy a biztonsági másolatok működnek az új gyorsítótárhelyen, az eredeti gyorsítótárhelyet eltávolíthatja.
3. Frissítse a következő beállításjegyzékbeli bejegyzéseket az új gyorsítótárhely mappájának elérési útjával.<br/>

    | Beállításjegyzékbeli elérési út | Beállításjegyzék kulcsa | Érték |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Új gyorsítótár-mappa helye* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Új gyorsítótár-mappa helye* |

4. Indítsa újra a Backup motort úgy, hogy végrehajtja a következő parancsot egy rendszergazdai jogú parancssorban:

    ```PS C:\> Net start obengine```

Miután a biztonsági mentés létrehozása sikeresen befejeződött az új gyorsítótárhelyen, eltávolíthatja az eredeti gyorsítótármappát.


### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Hová helyezhetem el az Azure Backup-ügynök gyorsítótármappáját, hogy megfelelően működjön?<br/>
A következő helyek nem ajánlottak a gyorsítótármappa számára:

* Hálózati megosztás vagy cserélhető adathordozó: A gyorsítótármappának helyinek kell lennie az online biztonsági mentés használatával mentést igénylő kiszolgáló számára. A hálózati helyeket vagy cserélhető adathordozóra, például USB-meghajtók nem támogatottak.
* Offline kötetek: A gyorsítótármappa kell lennie az online biztonsági mentés várt Azure Backup szolgáltatás ügynökének használatával

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>Vannak a gyorsítótármappának olyan attribútumai, amelyek nem támogatottak?<br/>
A következő attribútumok vagy kombinációik nem támogatottak a gyorsítótármappa számára:

* Titkosított
* Deduplikált
* Tömörített
* Ritka
* Újraelemzési pont

Sem a gyorsítótármappa, sem a metaadatok virtuális merevlemez-meghajtója nem rendelkezik a szükséges attribútumokkal az Azure Backup-ügynökhöz.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Van mód a Backup szolgáltatás által használt sávszélesség nagyságának beállítására?<br/>
  Igen, használhatja a Backup ügynök **Tulajdonságok módosítása** beállítását a sávszélesség módosításához. Beállíthatja a sávszélesség méretét, továbbá az időpontokat, amikor használja ezt a sávszélességet. A részletes útmutatást lásd: **[Hálózatszabályozás engedélyezése](backup-configure-vault.md#enable-network-throttling)**.

## <a name="manage-backups"></a>Biztonsági másolatok kezelése
### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Mi történik, ha átnevezek egy Windows-kiszolgálót, amelyről biztonsági adatmentés történik az Azure rendszerbe?<br/>
Ha átnevez egy kiszolgálót, minden aktuálisan konfigurált biztonsági mentés leáll. Regisztrálja a kiszolgáló új nevét a Backup-tárolóban. Ha regisztrálta az új nevet a tárolóval, az első biztonsági mentési művelet egy *teljes* biztonsági mentés lesz. Ha helyre kell állítania a régi kiszolgálónévvel a tárolóba mentett adatokat, használja az **Adatok helyreállítása** varázsló [**Másik kiszolgáló**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) beállítását.

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Mi a fájl elérési útjának maximális hossza, amely megadható az Azure Backup-ügynök használatával a Backup szabályzatában? <br/>
Az Azure Backup ügynök az NTFS-re hagyatkozik. A [fájl elérési útvonalának hossz-specifikációját a Windows API korlátozza](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Ha a védeni kívánt fájlok hosszabb elérési úttal rendelkeznek, mint a Windows API által engedélyezett korlát, a biztonsági mentést a szülőmappáról vagy a lemezmeghajtóról készítse.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Mely karakterek engedélyezettek az Azure Backup ügynököt használó Azure Backup házirend elérési útjában? <br>
 Az Azure Backup ügynök az NTFS-re hagyatkozik. Ez engedélyezi az [NTFS által támogatott karakterek](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) használatát a fájl meghatározásának részeként. 
 
### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>Továbbra is megjelenik „Az Azure Backup nem lett konfigurálva ezen a kiszolgálón” figyelmeztetés annak ellenére, hogy már konfiguráltam biztonsági mentési szabályzatot <br/>
Ez a figyelmeztetés akkor jelenik meg, amikor a helyi kiszolgálón tárolt ütemezett biztonsági mentési beállítások nem egyeznek meg a biztonsági mentési tárolóban tárolt beállításokkal. Ha a kiszolgáló vagy a beállítások egy korábbi hibátlan állapotra lettek visszaállítva, a biztonsági mentés ütemezése elveszítheti a szinkronizálást. Ha ilyen figyelmeztetést kap, [konfigurálja újra a biztonsági mentési házirendet](backup-azure-manage-windows-server.md), majd kattintson a **Biztonsági mentés azonnali futtatása** parancsra a helyi kiszolgálónak az Azure szolgáltatással történő újraszinkronizálásához.
