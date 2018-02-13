---
title: "Az Azure Site Recovery hibaelhárítása az Azure-Azure replikációjával kapcsolatos problémák és hibák |} Microsoft Docs"
description: "Vészhelyreállítás Azure virtuális gépek replikálása hibáinak és problémáinak elhárítása"
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: sujayt
ms.openlocfilehash: be43e34976682847c4756e062ec5b638ebc26063
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure-Azure virtuális gép replikálási problémák elhárítása

Ez a cikk ismerteti az Azure Site Recovery replikálódik, és az Azure virtuális gépek helyreállítani egy régió tartozik egy másik régióban gyakori problémákat és azok megoldását ismerteti. Támogatott konfigurációk kapcsolatos további információkért tekintse meg a [támogatási mátrixot az Azure virtuális gépek replikálása](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure-erőforrás kvótájának problémák (hibakód: 150097)
Az előfizetés engedélyezni kell a cél régióban, a vész-helyreállítási régió használni kívánt Azure virtuális gépek létrehozásához. Emellett az előfizetés adott méretű virtuális gépek létrehozásához engedélyezett kvóta elegendő kell rendelkeznie. Alapértelmezés szerint a Site Recovery választja ki a cél virtuális gép méretének virtuális gép forrásaként. Ha a megfelelő méret nem érhető el, a legközelebbi lehetséges mérete automatikusan nek. Ha nincsenek egyező méretét, amely támogatja a forrás Virtuálisgép-konfiguráció, ez a hibaüzenet jelenik meg:

Hibakód: | **Lehetséges okok** | A javaslat
--- | --- | ---
150097<br></br>**Üzenet**: nem sikerült engedélyezni a replikálást a virtuális gép VmName. | -Az előfizetés-azonosító nem elképzelhető, hogy engedélyezve a célhelyen régió bármely virtuális gépek létrehozásához.</br></br>-Az előfizetés-azonosító nincs engedélyezve, vagy nem rendelkezik elegendő kvótával régió célhelyet a megadott Virtuálisgép-méretek létrehozásához.</br></br>-A megfelelő cél virtuális gép méretét, amely megfelel a forrás virtuális gép hálózati száma (2) a régió célhelyen az előfizetés-azonosító nem található.| Ügyfél [Azure számlázási támogatás segítségét](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) a szükséges Virtuálisgép-méretek a célhelyen az előfizetéshez tartozó virtuális gép létrehozásának engedélyezéséhez. Az engedélyezése után próbálkozzon újra a sikertelen műveletet.

### <a name="fix-the-problem"></a>A probléma elhárításához
Felveheti a kapcsolatot [Azure számlázási támogatás segítségét](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) ahhoz, hogy az előfizetés szükséges méretű virtuális gépek létrehozásához a célhelyen.

Ha a célhely kapacitás korlátozást tartalmaz, tiltsa le a replikációt, majd engedélyezze egy másik helyre, ahol az előfizetés van-e a szükséges méretű virtuális gépek létrehozásához elegendő kvótával.

## <a name="trusted-root-certificates-error-code-151066"></a>Megbízható legfelső szintű tanúsítványok (hibakód: 151066)

Ha a legfrissebb megbízható legfelső szintű tanúsítványok nem található meg a virtuális Gépet, az "Engedélyezés replikációs" munkahelye sikertelen lehet. A tanúsítványok nélkül Ha a hitelesítése és engedélyezése a Site Recovery szolgáltatás hívások a virtuális gép sikertelen. "A replikáció engedélyezése" Helyhelyreállítás sikertelen feladat a hibaüzenet jelenik meg:

Hibakód: | **Lehetséges ok** | **Javaslatok**
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

2.  Futtassa ezt a parancsot a könyvtár módosításához.

      ``# cd /etc/ssl/certs``

3. Ellenőrizze, hogy a Symantec legfelső szintű Hitelesítésszolgáltatói tanúsítvány jelen-e.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Ha a Symantec legfelső szintű Hitelesítésszolgáltatói tanúsítvány nem található, a következő parancsot a fájl letöltéséhez. Keressen hibákat, és kövesse a hálózati hibák javasolt műveletet.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Ellenőrizze, hogy a Baltimore legfelső szintű Hitelesítésszolgáltatói tanúsítvány jelen-e.

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Ha a Baltimore legfelső szintű Hitelesítésszolgáltatói tanúsítvány nem található, töltse le a tanúsítványt.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. Ellenőrizze, hogy a DigiCert_Global_Root_CA tanúsítvány jelen-e.

    ``# ls DigiCert_Global_Root_CA.pem``

8. Ha a DigiCert_Global_Root_CA nem található, a következő parancsokat a tanúsítvány letöltése.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. Parancsprogrammal rehash frissíteni a tanúsítvány tulajdonos kivonatok az újonnan letöltött tanúsítványok.

    ``# c_rehash``

10. Ellenőrizze, hogy ha a tulajdonos csak symlinks a tanúsítványok létrehozásához szükségesek.

    - Parancs

      ``# ls -l | grep Baltimore``

    - Kimenet

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Parancs

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Kimenet

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Parancs

      ``# ls -l | grep DigiCert_Global_Root``

    - Kimenet

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

11. Hozzon létre a fájlról VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem fájlnév b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

12. Hozzon létre a fájlról Baltimore_CyberTrust_Root.pem fájlnév 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Hozzon létre a fájlról DigiCert_Global_Root_CA.pem fájlnév 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Ellenőrizze, hogy ha a fájlok találhatók.  

    - Parancs

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Kimenet

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Kimenő kapcsolatok a Site Recovery URL-címek vagy IP-címtartományok (hibakód: 151037 vagy 151072)

A Site Recovery replikációs működnek, a kimenő kapcsolat számára megadott URL-címek vagy IP-címtartományok szükség a virtuális gépről. Ha a virtuális Gépet egy tűzfal mögött található, vagy a hálózati biztonsági csoport (NSG) szabályok segítségével szabályozza a kimenő kapcsolat, láthatja a hibaüzenetek:

**Hibakódok** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
151037<br></br>**Üzenet**: nem sikerült regisztrálni a virtuális gép az Azure Site Recovery szolgáltatással. | -Segítségével NSG kimenő hozzáférést a virtuális gép és a szükséges IP-címtartományok nem szerepel az engedélyezési listán a kimenő hozzáféréshez.</br></br>-Használata külső tűzfal eszközök, és a szükséges IP tartományok/URL-címei nem szerepel az engedélyezési listán.</br>| – Ha a tűzfal-proxy segítségével szabályozhatja a kimenő hálózati kapcsolat a virtuális Gépre, győződjön meg arról, hogy az előfeltételként szükséges URL-címek vagy datacenter IP-címtartományokat szerepel az engedélyezési listán. További információ: [proxy útmutatást tűzfal](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>-Ha NSG-szabályok segítségével szabályozhatja a kimenő hálózati kapcsolat a virtuális Gépre, győződjön meg arról, hogy az előfeltételként szükséges datacenter IP-címtartományok szerepel az engedélyezési listán. További információ: [hálózati biztonsági csoport útmutatást](https://aka.ms/a2a-nsg-guidance).
151072<br></br>**Üzenet**: a Site Recovery konfigurálása nem sikerült. | Nem sikerült csatlakozni a Site Recovery szolgáltatás végpontok. | – Ha a tűzfal-proxy segítségével szabályozhatja a kimenő hálózati kapcsolat a virtuális Gépre, győződjön meg arról, hogy az előfeltételként szükséges URL-címek vagy datacenter IP-címtartományokat szerepel az engedélyezési listán. További információ: [proxy útmutatást tűzfal](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>-Ha NSG-szabályok segítségével szabályozhatja a kimenő hálózati kapcsolat a virtuális Gépre, győződjön meg arról, hogy az előfeltételként szükséges datacenter IP-címtartományok szerepel az engedélyezési listán. További információ: [hálózati biztonsági csoport útmutatást](https://aka.ms/a2a-nsg-guidance).

### <a name="fix-the-problem"></a>A probléma elhárításához
Az engedélyezési lista [a szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [IP-címtartományok szükséges](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), kövesse a [hálózati útmutató](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>A lemez nem található a számítógépen (hibakód: 150039)

A virtuális Géphez csatlakozik, az új lemez inicializálni kell.

Hibakód: | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
150039<br></br>**Üzenet**: egy megfelelő lemezt a virtuális Gépet, amelynek az ugyanazon logikai egység értéke belül jelentett a nem megfeleltetni az Azure data lemez (DiskName) (DiskURI) rendelkező logikai egységen (LUN) (LUNValue). | – Új adatlemezt lett csatolva a virtuális gép, de az nem lett inicializálva.</br></br>– A virtuális Gépen belül a adatlemez megfelelően nem jelent a logikai érték, ahol a lemezt a virtuális Géphez lett csatolva.| Győződjön meg arról, hogy az adatlemezek inicializálása, majd próbálja megismételni a műveletet.</br></br>Windows: [Attach és az új lemez inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Linux: [lévő Linux új adatlemezt inicializálása](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>A probléma elhárításához
Győződjön meg arról, hogy az adatlemezek inicializálása megtörtént-e, és próbálkozzon újra a művelettel:

- Windows: [Attach és az új lemez inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Linux: [adja hozzá egy új adatlemez Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Ha a probléma továbbra is fennáll, forduljon a támogatási szolgálathoz.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Nem látja az Azure virtuális gép kiválasztható a "replikáció engedélyezése"

Ha nem látja a virtuális gép replikációs engedélyezni szeretné, akkor előfordulhat, hogy miatt elavult Site Recovery konfigurálása hagyható az Azure virtuális gépen. Az elavult konfigurálása sikerült hagyható egy Azure virtuális gépen a következő esetekben:

- Azure virtuális gép replikációjának engedélyezve a Site Recovery segítségével, és anélkül, hogy explicit módon a replikáció a virtuális Gépre letiltása törli a Site Recovery-tárolóban.
- Azure virtuális gép replikációjának engedélyezve a Site Recovery segítségével, és a Site Recovery-tároló nélkül explicit módon a replikáció a virtuális Gépre letiltása tartalmazó erőforráscsoportot törölt.

### <a name="fix-the-problem"></a>A probléma elhárításához

Használhat [távolítsa el az elavult automatikus konfigurációs parancsfájl](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) , és távolítsa el az elavult Site Recovery konfigurálása az Azure virtuális gépen. Megtekintheti a virtuális gép eltávolítása a elavult konfigurálása után kell lennie.

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>A virtuális gép üzembe helyezési állapota érvénytelen (hibakód: 150019)

Ahhoz, hogy a replikáció a virtuális Gépre, a telepítés állapotát kell **sikeres**. Az alábbi lépéseket követve ellenőrizheti a virtuális gép állapota.

1.  Válassza ki a **erőforrás-kezelő** a **minden szolgáltatás** Azure-portálon.
2.  Bontsa ki a **előfizetések** listában, és jelölje ki az előfizetését.
3.  Bontsa ki a **ResourceGroups** listában, és válassza ki az erőforráscsoportot, a virtuális gép.
4.  Bontsa ki a **erőforrások** listában, és válassza ki a virtuális gépet
5.  Ellenőrizze a **provisioningState** mező mellett a jobb oldali példányait tartalmazó nézetet.

### <a name="fix-the-problem"></a>A probléma elhárításához

- Ha **provisioningState** van **sikertelen**, forduljon a támogatási szolgálathoz elhárítása adatokkal.
- Ha **provisioningState** van **Frissítéskísérleti**, egy másikat is első telepíthető. Ellenőrizze, hogy van-e a virtuális Gépre, várjon, amíg befejeződik, majd próbálja megismételni a sikertelen helyreállítás, hogy a folyamatban lévő műveleteket **engedélyezze a replikálást** feladat.

## <a name="next-steps"></a>További lépések
[Azure-alapú virtuális gépek replikálása](site-recovery-replicate-azure-to-azure.md)
