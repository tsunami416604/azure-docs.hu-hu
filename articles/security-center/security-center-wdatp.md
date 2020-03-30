---
title: Microsoft Defender komplex veszélyforrások elleni védelem – Azure Security Center
description: Ez a dokumentum bemutatja az Azure Security Center és a Microsoft Defender komplex veszélyforrások elleni védelem közötti integrációt.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 13852acb39a420e2f0da84e18bef4df823c1fa78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206265"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender komplex veszélyforrások elleni védelem az Azure Security Centerrel

Az Azure Security Center a Microsoft Defender komplex [veszélyforrások elleni védelemmel](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP) való integrálásával bővíti felhőalapú számítási feladatok elleni platformjait.
Ez a változás átfogó végpontészlelési és -válaszolási (EDR) képességeket tesz lehetővé. A Microsoft Defender ATP-integrációval észlelheti a rendellenességeket. Az Azure Security Center által figyelt kiszolgálóvégpontok elleni speciális támadások észlelése és azokra való reagálás.

## <a name="microsoft-defender-atp-features-in-security-center"></a>A Microsoft Defender ATP szolgáltatásai a Security Centerben

A Microsoft Defender ATP használatakor a következőket kapja:

- **Speciális, behatolás utáni észlelési érzékelők**: A Microsoft Defender ATP-érzékelői a Windows-kiszolgálókhoz viselkedési jelek széles skáláját gyűjtik.

- **Analytics-alapú, felhőalapú utórés észlelése:** A Microsoft Defender ATP gyorsan alkalmazkodik a változó fenyegetésekhez. Fejlett elemzéseket és big data-adatokat használ. A Microsoft Defender ATP-t az intelligens biztonsági grafikon windowsos, Azure-beli és Office-beli jelekkel erősíti az ismeretlen fenyegetések észlelése. Végrehajtható riasztásokat biztosít, és lehetővé teszi a gyors reagálást.

- **Fenyegetésfelderítés**: A Microsoft Defender ATP riasztásokat hoz létre, amikor támadó eszközöket, technikákat és eljárásokat azonosít. A Microsoft fenyegetésvadászok és biztonsági csapatok által létrehozott adatokat használja, amelyeket a partnerek által biztosított intelligencia egészít ki.

Az Azure Security Centerben már elérhetők a következő képességek:

- **Automatikus bevezetés:** A Microsoft Defender ATP-érzékelő automatikusan engedélyezve van az Azure Security Centerbe beszállt Windows-kiszolgálókon.

- **Egyetlen üvegtábla:** Az Azure Security Center konzol microsoft defender ATP-riasztásokat jelenít meg.

- **Részletes gépvizsgálat:** Az Azure Security Center-ügyfelek a Microsoft Defender ATP-konzol segítségével részletes vizsgálatot végezhetnek a szabályszegés hatókörének feltárása érdekében.

![Azure Security Center, az egyes riasztásokra vonatkozó riasztások és általános információk listájának megjelenítése](media/security-center-wdatp/image1.png)

További vizsgálathoz használja a Microsoft Defender ATP.To investigate further, use Microsoft Defender ATP. A Microsoft Defender ATP további információkat, például a riasztási folyamat fa és az incidens grafikon. Egy részletes gépi idővonalat is láthat, amely egy legfeljebb hat hónapos előzményviselkedést jelenít meg.

![A Microsoft Defender ATP-oldala részletes információkkal rendelkezik a riasztásról](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platformtámogatás

A Microsoft Defender ATP a Security Centerben támogatja a Windows Server 2016, 2012 R2 és 2008 R2 SP1 rendszeren történő észlelést, az Azure-beli virtuális gépekhez standard szintű előfizetésre van szükség, a nem Azure-beli virtuális gépekhez pedig csak a munkaterület i szintjén van szükség standard szintű szintre.

> [!NOTE]
> Ha az Azure Security Center használatával figyeli a kiszolgálókat, a Rendszer automatikusan létrehozza a Microsoft Defender ATP-bérlőt, és alapértelmezés szerint európában tárolja a Microsoft Defender ATP-adatait. Ha át kell helyeznie az adatokat egy másik helyre, a bérlő alaphelyzetbe állításához forduljon a Microsoft támogatási szolgálatához. Az integrációt használó kiszolgálóvégpont-figyelés le van tiltva az Office 365 GCC-ügyfelei számára.

## <a name="onboarding-servers-to-security-center"></a>Kiszolgálók előkészítése a Security Centerre 

Ha a kiszolgálókat a Security Centerbe szeretné átszállni, kattintson az Ugrás az Azure Security Centerbe a Microsoft Defender ATP-kiszolgáló **bevezetési kiszolgálóinak fedélzeti kiszolgálóihoz** elemre.

1. A **Bevezetés** területen jelöljön ki vagy hozzon létre egy munkaterületet, ahol az adatokat tárolni szeretné. <br>
2. Ha nem látja az összes munkaterületet, annak oka lehet az engedélyek hiánya, győződjön meg arról, hogy a munkaterület az Azure Security Standard tier. További információt a [Frissítés a Biztonsági központ standard szintjére a fokozott biztonság érdekében](security-center-pricing.md)című témakörben talál.
    
3. Válassza **a Kiszolgálók hozzáadása** lehetőséget a Microsoft Monitoring Agent telepítésével kapcsolatos utasítások megtekintéséhez. 

4. A bevezetés után figyelheti a gépeket a **Számítási és alkalmazások**csoportban.

   ![Beépített számítógépek](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>A Microsoft Defender ATP-integráció engedélyezése

Ha meg szeretné tekinteni, hogy a Microsoft Defender ATP-integráció engedélyezve van-e, válassza a **Biztonsági központ** > **díjszabása & beállítások lehetőséget,** > kattintson az előfizetésére.
Itt láthatja a jelenleg engedélyezett integrációkat.

  ![Az Azure Security Center fenyegetésészlelési beállításailap, amelyen engedélyezve van a Microsoft Defender ATP-integráció](media/security-center-wdatp/enable-integrations.png)

- Ha már beszállt a kiszolgálók az Azure Security Center standard szintű, nem kell további lépéseket tennie. Az Azure Security Center automatikusan bevezeti a kiszolgálókat a Microsoft Defender ATP szolgáltatásba. A bevezetés akár 24 órát is igénybe vehet.

- Ha még soha nem volt berakva a kiszolgálókat az Azure Security Center standard szintű szintjére, a szokásos módon az Azure Security Centerbe kell beszállnia őket.

- Ha a Microsoft Defender ATP-n keresztül szállt be a kiszolgálókra:
  - A [kiszolgálógépek külső fedélzeti szolgáltatására](https://go.microsoft.com/fwlink/p/?linkid=852906)vonatkozó útmutatást a dokumentációban találja.
  - Ezeket a kiszolgálókat az Azure Security Centerbe.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Hozzáférés a Microsoft Defender ATP portálhoz

Kövesse a [Felhasználói hozzáférés hozzárendelése a portálhoz](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)című, a felhasználói hozzáférés hozzárendelése című útmutató utasításait.

## <a name="set-the-firewall-configuration"></a>A tűzfal konfigurációjának beállítása

Ha olyan proxyval vagy tűzfallal rendelkezik, amely blokkolja a névtelen forgalmat, mivel a Microsoft Defender ATP-érzékelő a rendszerkörnyezetből csatlakozik, győződjön meg arról, hogy a névtelen forgalom engedélyezett. Kövesse a [Microsoft Defender ATP szolgáltatás URL-címének engedélyezése a proxykiszolgálón](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)található Utasításokat.

## <a name="test-the-feature"></a>A funkció tesztelése

Jóindulatú Microsoft Defender ATP-tesztriasztás létrehozása:

1. Hozzon létre egy mappát "C:\test-MDATP-teszt".

1. A Távoli asztal segítségével windows Server 2012 R2 virtuális gép vagy Windows Server 2016 virtuális gép érhető el. Nyisson meg egy parancssori ablakot.

1. A parancssorba másolja és futtassa a következő parancsot. A parancssor ablak automatikusan bezárul.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Parancssorablak a fenti paranccsal](media/security-center-wdatp/image4.jpeg)

3. Ha a parancs sikeres, megjelenik egy új riasztás az Azure Security Center irányítópultján és a Microsoft Defender ATP portálon. A riasztás megjelenése eltarthat néhány percig.

4. A biztonsági központban a riasztás áttekintéséhez nyissa meg a**Gyanús PowerShell-parancsvonal**biztonsági **riasztásokat.** > 

5. A vizsgálat ablakban válassza ki a hivatkozást a Microsoft Defender ATP portálra való ugráshoz.

## <a name="next-steps"></a>További lépések

- [Az Azure Security Center által támogatott platformok és szolgáltatások](security-center-os-coverage.md)
- [Biztonsági szabályzatok beállítása az Azure Security Centerben:](tutorial-security-policy.md)Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és erőforráscsoportok biztonsági szabályzatait.
- [Biztonsági javaslatok kezelése az Azure Security Centerben:](security-center-recommendations.md)Ismerje meg, hogyan segíthetnek a javaslatok az Azure-erőforrások védelmében.
- [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md): Útmutató az Azure-erőforrások állapotának monitorozásához.
