---
title: A biztonsági szolgáltatások áttekintése
description: Ismerje meg az Azure Backup biztonsági kameráinak biztonsági funkcióit, amelyek segítenek a biztonsági mentési adatok védelmében és a vállalkozás biztonsági igényeinek kielégítésében.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 2eec3ee50f1de695b5432ee50b0900e35b81a6eb
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585819"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Az Azure Backup biztonsági szolgáltatásainak áttekintése

Az adatok védelme érdekében az egyik legfontosabb lépés egy megbízható biztonsági mentési infrastruktúra. De ugyanolyan fontos annak biztosítása, hogy az adatok ról biztonsági mentésbiztonságos módon, és hogy a biztonsági mentések mindig védett. Az Azure Backup biztonságot nyújt a biztonsági mentési környezetben – mind az adatok átvitele közben, mind az inaktív állapotban. Ez a cikk az Azure Backup biztonsági mentési funkcióit sorolja fel, amelyek segítenek a biztonsági mentési adatok védelmében és a vállalkozás biztonsági igényeinek kielégítésében.

## <a name="management-and-control-of-identity-and-user-access"></a>Az identitás és a felhasználói hozzáférés kezelése és ellenőrzése

Az Azure Backup lehetővé teszi a részletes hozzáférés kezelését az [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)használatával. Az RBAC lehetővé teszi a feladatok elkülönítését a csapaton belül, és csak a munkájukhoz szükséges felhasználók számára biztosíthatja a hozzáférést.

Az Azure Backup három beépített szerepkört biztosít a biztonsági mentéskezelési műveletek vezérléséhez:

* Biztonsági másolat közreműködője - biztonsági mentések létrehozása és kezelése, kivéve a Recovery Services-tároló törlését és a másokhoz való hozzáférést
* Biztonságimásolat-készítő – minden, amit egy közreműködő tesz, kivéve a biztonsági mentési házirendek eltávolítását és kezelését
* Biztonságimásolat-olvasó – az összes biztonságimentés-kezelési művelet megtekintésére vonatkozó engedélyek

További információ az Azure Backup kezeléséhez a [szerepköralapú hozzáférés-vezérlésről.](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)

Az Azure Backup számos biztonsági vezérlőt épített a szolgáltatásba a biztonsági rések megelőzése, észlelése és megválaszolása érdekében. További információ [az Azure Backup biztonsági vezérlőiről.](https://docs.microsoft.com/azure/backup/backup-security-controls)

## <a name="separation-between-guest-and-azure-storage"></a>A vendég- és az Azure-tároló elkülönítése

Az Azure Backup, amely magában foglalja a virtuális gép biztonsági mentésés az SQL és az SAP HANA virtuális gép biztonsági mentés, a biztonsági mentési adatok az Azure storage-ban tárolják, és a vendég nem rendelkezik közvetlen hozzáféréssel a biztonsági mentési tároló vagy annak tartalmát.  A virtuális gép biztonsági mentésével a biztonsági mentési pillanatkép létrehozása és tárolása az Azure-háló, ahol a vendég nem vesz részt más, mint az alkalmazás konzisztens biztonsági mentések számítási feladatának lefizetése.  Az SQL és az SAP HANA, a biztonsági mentési bővítmény ideiglenes hozzáférést kap az adott blobok írásához.  Ily módon, még egy sérült környezetben, a meglévő biztonsági mentések nem lehet manipulálni, vagy törölni a vendég.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Az Azure vm biztonsági mentéséhez nem szükséges internetkapcsolat

Az Azure virtuális gépek biztonsági mentése az adatok áthelyezése a virtuális gép lemezéről a Recovery Services-tárolóba. Azonban az összes szükséges kommunikáció és adatátvitel csak az Azure gerinchálózatán történik anélkül, hogy a virtuális hálózat eléréséhez. Ezért a biztonságos hálózatokon belül elhelyezett Azure virtuális gépek biztonsági mentése nem követeli meg, hogy hozzáférést biztosítson az IP-khez vagy a teljes tartománynhálózatokhoz.

## <a name="private-endpoints-for-azure-backup"></a>Privát végpontok az Azure biztonsági mentéséhez

Most már [használhatja a privát végpontok](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) biztonsági másolatot az adatokbiztonságosan a kiszolgálók egy virtuális hálózaton belül a Recovery Services-tároló. A privát végpont a virtuális hálózat címterületéről származó IP-címet használ a tárolóhoz, így nem kell a virtuális hálózatokat nyilvános IP-kszámára elérhetővé tenni. A privát végpontok az Azure-beli virtuális gépeken futó SQL- és SAP HANA-adatbázisok biztonsági mentéséhez és visszaállításához használhatók. Azt is fel lehet használni a helyszíni kiszolgálók a MARS-ügynök használatával.

>[!NOTE]
> Ez a funkció jelenleg korlátozott annektál. Kérjük, töltse ki [ezt a felmérést,](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) és [írjon nekünk,](mailto:azbackupnetsec@microsoft.com) ha szeretné használni a privát végpontok az Azure Backup. A funkció használatának lehetőségét az Azure Backup szolgáltatás jóvá kell hagynia.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Az átvitel és nyugalom alatt lévő adatok titkosítása

A titkosítás védi az adatokat, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az Azure-on belül az Azure storage és a tároló közötti átvitel alatt álló adatokhttps-védelemmel rendelkeznek. Ezek az adatok az Azure gerinchálózatán maradnak.

* A biztonsági mentési adatok automatikusan titkosítva vannak a Microsoft által kezelt kulcsokkal. A biztonsági mentésben szereplő felügyelt lemezes virtuális gépeket is titkosíthatja a Recovery Services Vaultban az Azure Key Vaultban tárolt [ügyfél által](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) kezelt kulcsok használatával. A titkosítás engedélyezéséhez nem kell semmilyen explicit műveletet végrehajtania. Ez a Recovery Services-tárolóba biztonsági mentést le, minden számítási feladatra vonatkozik.

* Az Azure Backup támogatja az Azure-beli virtuális gépek biztonsági mentését és visszaállítását, amelyek operációsrendszer-/adatlemezeit Azure Disk Encryption (ADE) titkosítással titkosítják. [További információ a titkosított Azure-virtuális gépekről és az Azure Backup ról.](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>A biztonsági másolat adatainak védelme a nem szándékos törlésekkel szemben

Az Azure Backup biztonsági funkciókat biztosít a biztonsági mentési adatok védelme érdekében még a törlés után is. A helyreállítható törlés, ha a felhasználó törli a biztonsági másolatot a virtuális gép, a biztonsági mentési adatok megmaradnak további 14 napig, amely lehetővé teszi a biztonsági mentési elem helyreállítása adatvesztés nélkül. A biztonsági mentési adatok "helyreállítható törlés" állapotban való további 14 napos megőrzése nem jár semmilyen költséggel az ügyfél számára. [További információ a helyreállítható törlésről.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete)

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Gyanús tevékenység figyelése és riasztásai

Az Azure Backup [beépített figyelési és riasztási lehetőségeket](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) biztosít az Azure Backup hoz kapcsolódó eseményekhez kapcsolódó műveletek megtekintéséhez és konfigurálásához. [A biztonsági mentési jelentések](https://docs.microsoft.com/azure/backup/configure-reports) egyablakos célként szolgálnak a használat nyomon követéséhez, a biztonsági mentések és -visszaállítások naplózásához, valamint a legfontosabb trendek azonosításához a részletesség különböző szintjein. Az Azure Backup figyelési és jelentéskészítő eszközeivel riasztást adhat bármely jogosulatlan, gyanús vagy rosszindulatú tevékenységre, amint azok bekövetkeznek.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Biztonsági funkciók a hibrid biztonsági mentések védelmére

Az Azure Backup szolgáltatás a Microsoft Azure Recovery Services (MARS) ügynök segítségével biztonsági másolatot készít a fájlokról, mappákról, valamint a kötetről vagy a rendszerállapotról egy helyszíni számítógépről az Azure-ba. A MARS mostantól biztonsági funkciókat biztosít a hibrid biztonsági mentések védelméhez. Ezek a szolgáltatások a következők:

* A rendszer egy további hitelesítési réteget ad hozzá, amikor kritikus műveletet hajtanak végre, például egy jelszót módosítanak. Ez az ellenőrzés biztosítja, hogy az ilyen műveleteket csak érvényes Azure-hitelesítő adatokkal rendelkező felhasználók hajthassák végre. [További információ a támadásokat megakadályozó funkciókról.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks)

* A törölt biztonsági mentési adatok a törlés napjától számított további 14 napig maradnak meg. Ez biztosítja az adatok helyrehozhatóságát egy adott időszakon belül, így nincs adatvesztés akkor is, ha támadás történik. Emellett nagyobb számú minimális helyreállítási pontot tartanak fenn a sérült adatok elleni védelem érdekében. [További információ a törölt biztonsági mentési adatok helyreállításáról](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

* A Microsoft Azure Recovery Services (MARS) ügynök használatával biztonsági mentésre kerülő adatok esetében a rendszer egy jelszót használ annak biztosítására, hogy az adatok titkosítása az Azure Backup ba való feltöltés előtt titkosítva legyen, és csak az Azure Backup biztonsági rendszerből való letöltés után fejtse vissza. A jelszó részletei csak a jelszót létrehozó felhasználó és a vele konfigurált ügynök számára érhetők el. A szolgáltatással semmit sem továbbít a rendszer, illetve nem oszt meg. Ez biztosítja az adatok teljes biztonságát, mivel a véletlenül elérhető adatok (például a hálózat elleni véletlen ülecsű támadás) használhatatlanok a jelszó nélkül, és a jelmondat nem kerül elküldésre a hálózaton.

## <a name="compliance-with-standardized-security-requirements"></a>A szabványosított biztonsági követelményeknek való megfelelés

Annak érdekében, hogy a szervezetek megfeleljenek az egyének adatainak gyűjtésére és felhasználására vonatkozó nemzeti, regionális és iparág-specifikus követelményeknek, a Microsoft Azure & az Azure Backup a tanúsítványok és tanúsítványok átfogó készletét kínálja. [Tekintse meg a megfelelőségi tanúsítványok listáját](compliance-offerings.md)

## <a name="next-steps"></a>További lépések

* [Az Azure Backup ot használó felhőalapú számítási feladatok védelmére szolgáló biztonsági funkciók](backup-azure-security-feature-cloud.md)
* [Az Azure Backup biztonsági mentést használó hibrid biztonsági mentések védelmére szolgáló biztonsági funkciók](backup-azure-security-feature.md)
