---
title: "Készítsen biztonsági mentést a DPM-munkaterhelések számára a klasszikus Azure portálon |} Microsoft Docs"
description: "Az Azure Backup szolgáltatás használatával a DPM-kiszolgálók biztonsági másolatának bemutatása"
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
keywords: "A System Center Data Protection Manager, a data protection manager, a dpm biztonsági mentése"
ms.assetid: 8f23972b-d167-4231-b331-e198db3b18b4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: nkolli;giridham;markgal
ms.openlocfilehash: a9a516cfdfaf4b95c4f0121a66e90f6e71206e9f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Számítási feladatok Azure-ba való biztonsági mentésének előkészítése a DPM-mel
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Az Azure Backup Server (klasszikus)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (klasszikus)](backup-azure-dpm-introduction-classic.md)
>
>

Ez a cikk bemutatja azokat a Microsoft Azure Backup segítségével a System Center Data Protection Manager (DPM) kiszolgálók és munkaterhelések védelme érdekében. Az olvasásával lesz tisztában:

* Hogyan működik a Azure DPM-kiszolgáló biztonsági mentése
* Az Előfeltételek a zökkenőmentes biztonsági mentés élmény eléréséhez
* A szokásos hibákat észlelt, és azokat kezelése
* Támogatott esetek

A System Center DPM fájl-és alkalmazásadatok készít. Adatok biztonsági mentése a DPM a lemezen, szalagon tárolja, vagy biztonsági mentése az Azure-ba, a Microsoft Azure Backup szolgáltatásnál. A DPM a következőképpen együttműködik az Azure Backup szolgáltatással:

* **Fizikai kiszolgáló vagy a helyszíni virtuális gépként telepített DPM** – Ha a DPM telepítése fizikai kiszolgálóként vagy helyszíni Hyper-V virtuális gépként is adatok biztonsági mentését egy Azure Backup tárolóba mellett a lemezes és szalagos biztonsági mentés.
* **Az Azure virtuális gépként telepített DPM** – a System Center 2012 R2 Update 3, a DPM telepíthető Azure virtuális gépként. Ha a DPM biztonsági másolatot készíthet adatok Azure-os lemezekre Azure virtuális gépként van telepítve a DPM Azure virtuális géphez csatolt, vagy meg is kiszervezése az adattárolási számára azok történő biztonsági mentés az Azure Backup-tárolóban.

## <a name="why-backup-your-dpm-servers"></a>Miért érdemes biztonsági másolatot készíteni a DPM-kiszolgáló?
Az Azure Backup használatával biztonsági mentéséről a DPM-kiszolgálók üzleti előnyei a következők:

* A helyszíni DPM-telepítés használhatja az Azure biztonsági mentés szalagra hosszú távú központi telepítés helyett.
* A DPM az Azure-ban történő telepítés esetén Azure Backup szolgáltatás lehetővé teszi kiszervezése tárolás az Azure lemezről, lehetővé téve a tárolást a régebbi adatokat az Azure biztonsági mentés és a lemezen lévő új adatokat.

## <a name="how-does-dpm-server-backup-work"></a>Hogyan működik a DPM-kiszolgáló biztonsági mentése?
Biztonsági mentése a virtuális gép, először az adatok időpontban pillanatkép van szükség. Az Azure Backup szolgáltatás a biztonsági mentési feladatot indít el a megadott időpontban, és elindítja a tartalék mellék pillanatképének elkészítéséhez. A tartalék mellék koordinálja a vendégen VSS szolgáltatásban konzisztencia eléréséhez, és meghívja a blob pillanatkép API-t az Azure Storage szolgáltatás konzisztencia elérése után. Ez történik, nem kell leállítani egy lemezt a virtuális gép alkalmazáskonzisztens pillanatfelvételi eléréséhez.

Után kerül sor a pillanatkép, az adatátvitel által az Azure Backup szolgáltatás a biztonsági mentési tárolóba. A szolgáltatás azonosítására, és csak a legutóbbi biztonsági másolat és a biztonsági másolatok tárolási és hálózati hatékony módosított blokkok átvitele gondoskodik. Az adatátvitel befejezése után a rendszer eltávolítja a pillanatképet, és egy helyreállítási pontot hoz létre. Ez a helyreállítási pont látható a klasszikus Azure portálon.

> [!NOTE]
> Linux virtuális gépek esetében kizárólag fájlkonzisztens biztonsági mentés lehetőség.
>
>

## <a name="prerequisites"></a>Előfeltételek
Készítse elő az Azure biztonsági mentési adatainak biztonsági mentése a DPM az alábbiak szerint:

1. **Hozzon létre egy biztonsági mentési tárolót**. Ha az előfizetés még nem hozott létre a biztonsági másolatok tárolóját, tekintse meg a az Azure portál-verzió az ennek a cikknek - [-munkaterhelések biztonsági mentése az Azure-ba, a dpm-mel történő előkészítéséhez](backup-azure-dpm-introduction.md).

  > [!IMPORTANT]
  > 2017 márciusától már nem hozhat létre Backup-tárolókat a klasszikus portálon.
  > A biztonsági mentési tárakról mostantól lehetőség van helyreállítási tárakra váltani. A részletekről bővebben az [Váltás biztonsági mentési tárolóról Recovery Services-tárolóra](backup-azure-upgrade-backup-to-recovery-services.md) című cikkben olvashat. A Microsoft azt javasolja, hogy a biztonsági mentési tárolóról váltson Recovery Services-tárolóra.<br/> 2017. október 15-től a PowerShell nem használható Backup-tárolók létrehozására. **2017. november 1-től**:
  >- Minden fennmaradó Backup-tároló automatikusan Recovery Services-tárolóra frissül.
  >- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a Recovery Services-tárolókban található biztonsági mentési adatokhoz.
  >

2. **Töltse le a tárolói hitelesítő adatokat** – a Azure Backup szolgáltatásban töltse fel a létrehozott tanúsítványt a tárolóba.
3. **Telepítse az Azure Backup szolgáltatás ügynöke, és regisztrálja a kiszolgálót** – az Azure Backup szolgáltatásban, telepítse az ügynököt minden DPM-kiszolgálón, a DPM-kiszolgáló regisztrálása a mentési tároló.

[!INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[!INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## <a name="requirements-and-limitations"></a>Követelmények (és korlátozások)
* DPM fizikai kiszolgálóként vagy a System Center 2012 SP1 vagy a System Center 2012 R2 rendszeren telepített Hyper-V virtuális gép futtathat. Is futhat egy Azure virtuális gépként legalább fut a System Center 2012 R2 DPM 2012 R2 3. kumulatív frissítéssel vagy egy Windows virtuális gépként VMWare legalább fut a System Center 2012 R2 kumulatív frissítés 5.
* Ha a System Center 2012 SP1 DPM használ, telepítenie kell a System Center Data Protection Manager SP1 2. kumulatív frissítést. Erre azért szükség, az Azure Backup szolgáltatás ügynökének telepítése előtt.
* A DPM-kiszolgálóval kell rendelkeznie a Windows PowerShell és a .net Framework 4.5 telepítve.
* A DPM biztonsági másolatot készíthet a munkaterhelések többségéhez az Azure Backup szolgáltatásba. Teljes listáját lásd: Mi van támogatott az Azure Backup támogatja az alábbi elemek.
* Az Azure Backup tárolt adatokat nem lehet helyreállítani, a "Másolás szalagra" beállítással.
* Az Azure Backup szolgáltatás engedélyezve van az Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ a [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).
* Az Azure Backup használatához telepíteni kell a kiszolgálókat szeretne biztonsági másolatot készíteni az Azure Backup szolgáltatás ügynöke. Minden olyan kiszolgálón kell rendelkeznie, amelyek biztonsági mentése van folyamatban, szabad helyi tárterület érhető el az adatok mérete legalább 10 %. Például 10 GB szabad hely az ideiglenes helyen legalább 100 GB adat másolatának igényel. Bár a legalább 10 %, 15 % szabad helyi tárterület szükséges a gyorsítótárazáshoz használt ajánlott.
* A tároló Azure storage adatokat fog tárolni. Nincs korlátozva az adatok akkor is biztonsági mentés az Azure biztonsági mentési tároló van, de egy adatforrás (például egy virtuális géphez vagy adatbázis) mérete nem haladhatja meg a 54,400 GB.

Ilyen típusú biztonsági mentés Azure támogatottak:

* Titkosított (csak teljes biztonsági mentések)
* Tömörített (növekményes biztonsági mentések támogatva)
* Ritka (növekményes biztonsági mentések támogatva)
* Tömörített és ritka (Ritkaként kezelve)

És ezek nem támogatottak:

* A kis-és nagybetűket fájlrendszerek kiszolgálók nem támogatottak.
* A rögzített hivatkozások (kimaradnak)
* Újraelemzési pontok (kimaradnak)
* Titkosított és tömörített (kimaradnak)
* Titkosított és ritka (kimaradnak)
* Tömörített adatfolyam
* Ritka adatfolyam

> [!NOTE]
> A System Center 2012 DPM SP1-től, a készíthet biztonsági másolatot az Azure-bA Microsoft Azure Backup szolgáltatás használatával a DPM által védett munkaterhelések fel.
>
>
