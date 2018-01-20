---
title: "Az Azure Site Recovery hibaelhárítása az Azure-Azure replikációjával kapcsolatos problémák és hibák |} Microsoft Docs"
description: "Vészhelyreállítás Azure virtuális gépek replikálása hibáinak és problémáinak elhárítása"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: sujayt
ms.openlocfilehash: 76ec4f6d2f4471f2e8399c3e028321754ea7c160
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure-Azure virtuális gép replikálási problémák elhárítása

Ez a cikk ismerteti az Azure Site Recovery replikálódik, és az Azure virtuális gépek helyreállítani egy régió tartozik egy másik régióban gyakori problémákat és azok megoldását ismerteti. Támogatott konfigurációk kapcsolatos további információkért tekintse meg a [támogatási mátrixot az Azure virtuális gépek replikálása](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure-erőforrás kvótájának problémák (hibakód: 150097)
Az előfizetés engedélyezni kell a cél régióban, a vész-helyreállítási régió használni kívánt Azure virtuális gépek létrehozásához. Emellett az előfizetés adott méretű virtuális gépek létrehozásához engedélyezett kvóta elegendő kell rendelkeznie. Alapértelmezés szerint a Site Recovery választja ki a cél virtuális gép méretének virtuális gép forrásaként. Ha a megfelelő méret nem érhető el, a legközelebbi lehetséges mérete automatikusan nek. Ha nincsenek egyező méretét, amely támogatja a forrás Virtuálisgép-konfiguráció, ez a hibaüzenet jelenik meg:

**Hibakód:** | **Lehetséges okok** | **A javaslat**
--- | --- | ---
150097<br></br>**Üzenet**: nem sikerült engedélyezni a replikálást a virtuális gép VmName. | -Az előfizetés-azonosító nem elképzelhető, hogy engedélyezve a célhelyen régió bármely virtuális gépek létrehozásához.</br></br>-Az előfizetés-azonosító nincs engedélyezve, vagy nem rendelkezik elegendő kvótával régió célhelyet a megadott Virtuálisgép-méretek létrehozásához.</br></br>-A megfelelő cél virtuális gép méretét, amely megfelel a forrás virtuális gép hálózati száma (2) a régió célhelyen az előfizetés-azonosító nem található.| Ügyfél [Azure számlázási támogatás segítségét](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) a szükséges Virtuálisgép-méretek a célhelyen az előfizetéshez tartozó virtuális gép létrehozásának engedélyezéséhez. Az engedélyezése után próbálkozzon újra a sikertelen műveletet.

### <a name="fix-the-problem"></a>A probléma elhárításához
Felveheti a kapcsolatot [Azure számlázási támogatás segítségét](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) ahhoz, hogy az előfizetés szükséges méretű virtuális gépek létrehozásához a célhelyen.

Ha a célhely kapacitás korlátozást tartalmaz, tiltsa le a replikációt, majd engedélyezze egy másik helyre, ahol az előfizetés van-e a szükséges méretű virtuális gépek létrehozásához elegendő kvótával.

## <a name="trusted-root-certificates-error-code-151066"></a>Megbízható legfelső szintű tanúsítványok (hibakód: 151066)

Ha a legfrissebb megbízható legfelső szintű tanúsítványok nem található meg a virtuális Gépet, az "Engedélyezés replikációs" munkahelye sikertelen lehet. A tanúsítványok nélkül Ha a hitelesítése és engedélyezése a Site Recovery szolgáltatás hívások a virtuális gép sikertelen. "A replikáció engedélyezése" Helyhelyreállítás sikertelen feladat a hibaüzenet jelenik meg:

**Hibakód:** | **Lehetséges ok** | **Javaslatok**
--- | --- | ---
151066<br></br>**Üzenet**: a Site Recovery konfigurálása nem sikerült. | A szükséges megbízható legfelső szintű tanúsítványokat használ a hitelesítéshez és a hitelesítés nem található a számítógépen. | -A Windows operációs rendszert futtató virtuális gép, győződjön meg arról, hogy találhatók-e a megbízható legfelső szintű tanúsítványok a számítógépen. További információ: [konfigurálása a megbízható főtanúsítványok és nem engedélyezett tanúsítványok](https://technet.microsoft.com/library/dn265983.aspx).<br></br>-A Linux operációs rendszert futtató virtuális gép, kövesse az útmutatást a Linux operációs rendszer verziója forgalmazó által közzétett megbízható legfelső szintű tanúsítványok.

### <a name="fix-the-problem"></a>A probléma elhárításához
**Windows**

A legújabb Windows frissítések telepítése a virtuális Gépen, hogy a megbízható legfelső szintű tanúsítványok találhatók a számítógépen. Ha kapcsolat nélküli környezetben, hajtsa végre a szabványos Windows-frissítési folyamat beszerezheti a tanúsítványokat a szervezet. Ha a szükséges tanúsítványok nem található meg a virtuális Gépet, a Site Recovery szolgáltatáshoz intézett hívások biztonsági okok miatt sikertelen.

A tipikus Windows update felügyeleti vagy a tanúsítvány frissítéskezelési folyamat le a legfrissebb legfelső szintű tanúsítványok és a frissített visszavont tanúsítványok listáját a virtuális gépeken a szervezetében kövesse.

Győződjön meg arról, hogy a probléma megoldódott, lépjen a virtuális gép egy böngészőből login.microsoftonline.com.

**Linux**

Kövesse az útmutatást a virtuális Gépre a legújabb megbízható legfelső szintű tanúsítványok és a legújabb visszavont tanúsítványok listájának eléréséhez a Linux forgalmazó által biztosított.

Mivel az SuSE Linux symlinks tanúsítvány listának a karbantartására használja, kövesse az alábbi lépéseket:

1.  Jelentkezzen be egy legfelső szintű felhasználóként.

2.  Futtassa ezt a parancsot:

      ``# cd /etc/ssl/certs``

3.  Ha a Symantec legfelső szintű Hitelesítésszolgáltatói tanúsítvány jelen-e vagy sem megtekintéséhez futtassa a parancsot:

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4.  Ha a fájl nem található, futtassa az alábbi parancsokat:

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

      ``# c_rehash``

5.  Egy symlink létrehozása a b204d74a.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem, futtassa a parancsot:

      ``# ln -s  VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

6.  Ellenőrizze, hogy ha a parancsnak a következő kimenetet. Ha nem, akkor létre kell hoznia egy symlink:

      ``# ls -l | grep Baltimore
      -rw-r--r-- 1 root root   1303 Apr  7  2016 Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 04:47 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 05:01 653b494a.0 -> Baltimore_CyberTrust_Root.pem``

7. Ha symlink 653b494a.0 nincs telepítve, ez a parancs segítségével hozzon létre egy symlink:

      ``# ln -s Baltimore_CyberTrust_Root.pem 653b494a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Kimenő kapcsolatok a Site Recovery URL-címek vagy IP-címtartományok (hibakód: 151037 vagy 151072)

A Site Recovery replikációs működnek, a kimenő kapcsolat számára megadott URL-címek vagy IP-címtartományok szükség a virtuális gépről. Ha a virtuális Gépet egy tűzfal mögött található, vagy a hálózati biztonsági csoport (NSG) szabályok segítségével szabályozza a kimenő kapcsolat, láthatja a hibaüzenetek:

**Hibakódok** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
151037<br></br>**Üzenet**: nem sikerült regisztrálni a virtuális gép az Azure Site Recovery szolgáltatással. | -Segítségével NSG kimenő hozzáférést a virtuális gép és a szükséges IP-címtartományok nem szerepel az engedélyezési listán a kimenő hozzáféréshez.</br></br>-Használata külső tűzfal eszközök, és a szükséges IP tartományok/URL-címei nem szerepel az engedélyezési listán.</br>| – Ha a tűzfal-proxy segítségével szabályozhatja a kimenő hálózati kapcsolat a virtuális Gépre, győződjön meg arról, hogy az előfeltételként szükséges URL-címek vagy datacenter IP-címtartományokat szerepel az engedélyezési listán. További információ: [proxy útmutatást tűzfal](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>-Ha NSG-szabályok segítségével szabályozhatja a kimenő hálózati kapcsolat a virtuális Gépre, győződjön meg arról, hogy az előfeltételként szükséges datacenter IP-címtartományok szerepel az engedélyezési listán. További információ: [hálózati biztonsági csoport útmutatást](https://aka.ms/a2a-nsg-guidance).
151072<br></br>**Üzenet**: a Site Recovery konfigurálása nem sikerült. | Nem sikerült csatlakozni a Site Recovery szolgáltatás végpontok. | – Ha a tűzfal-proxy segítségével szabályozhatja a kimenő hálózati kapcsolat a virtuális Gépre, győződjön meg arról, hogy az előfeltételként szükséges URL-címek vagy datacenter IP-címtartományokat szerepel az engedélyezési listán. További információ: [proxy útmutatást tűzfal](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>-Ha NSG-szabályok segítségével szabályozhatja a kimenő hálózati kapcsolat a virtuális Gépre, győződjön meg arról, hogy az előfeltételként szükséges datacenter IP-címtartományok szerepel az engedélyezési listán. További információ: [hálózati biztonsági csoport útmutatást](https://aka.ms/a2a-nsg-guidance).

### <a name="fix-the-problem"></a>A probléma elhárításához
Az engedélyezési lista [a szükséges URL-címek](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-urls) vagy a [IP-címtartományok szükséges](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-ip-ranges), kövesse a [hálózati útmutató](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>A lemez nem található a számítógépen (hibakód: 150039)

A virtuális Géphez csatlakozik, az új lemez inicializálni kell.

**Hibakód:** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
150039<br></br>**Üzenet**: egy megfelelő lemezt a virtuális Gépet, amelynek az ugyanazon logikai egység értéke belül jelentett a nem megfeleltetni az Azure data lemez (DiskName) (DiskURI) rendelkező logikai egységen (LUN) (LUNValue). | – Új adatlemezt lett csatolva a virtuális gép, de az nem lett inicializálva.</br></br>– A virtuális Gépen belül a adatlemez megfelelően nem jelent a logikai érték, ahol a lemezt a virtuális Géphez lett csatolva.| Győződjön meg arról, hogy az adatlemezek inicializálása, majd próbálja megismételni a műveletet.</br></br>Windows: [Attach és az új lemez inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-portal#option-1-attach-and-initialize-a-new-disk).</br></br>Linux: [lévő Linux új adatlemezt inicializálása](https://docs.microsoft.com/azure/virtual-machines/linux/classic/attach-disk-classic#initialize-a-new-data-disk-in-linux).

### <a name="fix-the-problem"></a>A probléma elhárításához
Győződjön meg arról, hogy az adatlemezek inicializálása megtörtént-e, és próbálkozzon újra a művelettel:

- Windows: [Attach és az új lemez inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-portal#option-1-attach-and-initialize-a-new-disk).
- Linux: [lévő Linux új adatlemezt inicializálása](https://docs.microsoft.com/azure/virtual-machines/linux/classic/attach-disk-classic#initialize-a-new-data-disk-in-linux).

Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Nem látja az Azure virtuális gép kiválasztható a "replikáció engedélyezése"

Előfordulhat, hogy nem látja az Azure virtuális gép kiválasztható a [engedélyezze a replikálást: 2. lépés](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines). Probléma volt a Site Recovery elavult konfiguráció miatt hagyható az Azure virtuális gépen. Az elavult konfigurálása sikerült hagyható egy Azure virtuális gépen a következő esetekben:

- Azure virtuális gép replikációjának engedélyezve a Site Recovery segítségével, és anélkül, hogy explicit módon a replikáció a virtuális Gépre letiltása törli a Site Recovery-tárolóban.
- Azure virtuális gép replikációjának engedélyezve a Site Recovery segítségével, és a Site Recovery-tároló nélkül explicit módon a replikáció a virtuális Gépre letiltása tartalmazó erőforráscsoportot törölt.

### <a name="fix-the-problem"></a>A probléma elhárításához

Használhat [távolítsa el az elavult automatikus konfigurációs parancsfájl](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) , és távolítsa el az elavult Site Recovery konfigurálása az Azure virtuális gépen. A virtuális gép kell megjelennie [engedélyezze a replikálást: 2. lépés](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines) elavult konfigurációjának eltávolítása után.


## <a name="next-steps"></a>További lépések
[Azure-alapú virtuális gépek replikálása](site-recovery-replicate-azure-to-azure.md)
