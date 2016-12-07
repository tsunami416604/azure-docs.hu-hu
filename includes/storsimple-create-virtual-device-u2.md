#### <a name="to-create-a-virtual-device"></a>Virtuális eszköz létrehozása
1. Az Azure Portalon lépjen a **StorSimple Manager** szolgáltatáshoz.
2. Lépjen az **Eszközök** oldalra. Kattintson az **Eszközök** oldal alján található **Virtuális eszköz létrehozása** lehetőségre.
3. A **Virtuális eszköz létrehozása párbeszédpanelen** adja meg a következő részleteket.
   
    ![StorSimple virtuális eszköz létrehozása](./media/storsimple-create-virtual-device-u2/CreatePremiumsva1.png)
   
   1. **Név** – A virtuális eszköz egyedi neve.
   2. **Modell** – Válassza ki a virtuális eszköz modelljét. Ez a mező csak akkor jelenik meg, ha a 2. frissítést vagy újabb verziót futtat. Egy 8010-es eszközmodell 30 TB Standard szintű, míg egy 8020-as modell 64 TB Premium Storage tárhelyet biztosít. Ha biztonsági mentésekből szeretne elemszintű lekérési forgatókönyveket telepíteni, a 8010-es
   3.  modellt adja meg. A 8020-as modellel magas teljesítményű, alacsony késleltetésű munkaterheléseket telepíthet, vagy használhatja vészhelyreállítási másodlagos eszközként.
   4. **Verzió** – Válassza ki a virtuális eszköz verzióját. Ha a 8020-as modellt választja, akkor a Verzió mező nem jelenik meg a felhasználó számára. Ez a lehetőség akkor hiányzik, ha a szolgáltatáshoz regisztrált összes fizikai eszköz az 1. frissítést (vagy a rendszer újabb verzióját) futtatja. A mező csak akkor jelenik meg, ha a szolgáltatáshoz regisztrált az 1. frissítés előtti verziót és az 1. frissítést futtató fizikai eszközöket is. Mivel a virtuális eszköz verziója határozza meg, hogy melyik fizikai eszköz használható feladatátvételhez vagy klónozáshoz, fontos, hogy a virtuális eszköz megfelelő verzióját hozza létre. A következők szerint válasszon:
      
      * Válassza a 0.3-as frissítést futtató verziót, ha 0.3-as vagy korábbi verziójú fizikai eszközről hajt végre feladatátvételt vagy DR műveletet. 
      * Válassza az 1. frissítést futtató verziót, ha 1. vagy újabb verziójú fizikai eszközről hajt végre feladatátvételt vagy klónozást. 
   5. **Virtuális hálózat** – Adja meg a virtuális eszközzel használni kívánt virtuális hálózatot. Ha Premium Storage tárolást használ (2. frissítés vagy újabb), olyan virtuális hálózatot válasszon, amelyet a Premium szintű Storage-fiók támogat. A nem támogatott virtuális hálózatok szürkén jelennek meg a legördülő listában. Ha nem támogatott virtuális hálózatot választ ki, a rendszer figyelmezteti. 
   6. **Tárfiók a virtuális eszköz létrehozásához** – Válassza ki, hogy melyik tárfiókban kívánja tárolni a virtuális eszköz rendszerképét a kiépítés alatt. Ennek a tárfióknak ugyanabban a régióban kell lennie, ahol a virtuális eszköz és a virtuális hálózat is található. A tárfiókot sem fizikai, sem virtuális eszköz nem használhatja adattároláshoz. Alapértelmezés szerint erre a célra létrejön egy új tárfiók. Ha azonban tudja, hogy már van egy tárfiókja, amely alkalmas erre a célra, kiválaszthatja a listából. Ha létrehoz egy prémium szintű virtuális eszközt, a legördülő lista csak a Premium Storage-fiókokat jeleníti meg. 
      
      > [!NOTE]
      > A virtuális eszköz csak az Azure-tárfiókokkal működik. Az egyéb felhőszolgáltatók, mint például az Amazon, a HP és az OpenStack (amelyek fizikai eszközön támogatottak), a StorSimple virtuális eszközön nem támogatottak.
      > 
      > 
   7. Ha tudomásul vette, hogy a virtuális eszközön lévő adatokat egy Microsoft-adatközpont tárolja, kattintson a pipára. Amikor csak fizikai eszközt használ, a titkosítási kulcs az eszközön található, ezért a Microsoft nem tudja feloldani a titkosítást. 
      
       Amikor virtuális eszközt használ, a titkosítási és a visszafejtési kulcs tárolása is a Microsoft Azure-ban történik. További információkért tekintse meg a [virtuális eszközök használatára vonatkozó biztonsági szempontokat](../articles/storsimple/storsimple-security.md#storsimple-virtual-device-security).
   8. A virtuális eszköz létrehozásához kattintson a pipa ikonra. Az eszköz kiépítése körülbelül 30 percet vesz igénybe.
      
      ![StorSimple-virtuáliseszköz létrehozási fázisa](./media/storsimple-create-virtual-device-u2/StorSimple_VirtualDeviceCreating1M.png)



<!--HONumber=Nov16_HO3-->


