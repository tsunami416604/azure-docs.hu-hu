<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-the-storsimple-adapter-for-sharepoint"></a>A SharePoint a StorSimple-Adapter telepítése
1. A telepítő másolja a (WFE) előtér-webkiszolgálóján, a SharePoint központi felügyelet webalkalmazás futtatásához is konfigurálva van. 
2. Rendszergazdai jogosultságokkal rendelkező fiók használatával jelentkezzen be az ELŐTÉR-webkiszolgálón.
3. Kattintson duplán a telepítő. A StorSimple-adaptert SharePoint telepítővarázsló elindul. Kattintson a **tovább** a telepítés megkezdéséhez.
   
    ![StorSimple adapter telepítő start lap](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup1-include.png)
4. A SharePoint konfigurációs telepítőből StorSimple adapterén, adja meg egy telepítési helyét, írja be a DATA 0 hálózati adapterén IP-címét a StorSimple eszköz, és kattintson **következő**. 
   
    ![StorSimple adapter telepítés konfigurálása lapon](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup2-include.png) 
5. A telepítő megerősítési oldalán kattintson **telepítése**.
   
    ![StorSimple adapter telepítő visszaigazolási lapja](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Confirm_Setup-include.png) 
6. Kattintson a **Befejezés** a telepítővarázsló bezárásához.
   
    ![StorSimple adapter beállítása befejeződött lapon](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup_finish-include.png) 
7. Nyissa meg a SharePoint központi felügyelet lapján. Meg kell jelennie egy StorSimple konfigurációs csoportot, amely a SharePoint-hivatkozások a StorSimple-adaptert tartalmaz.
8. Nyissa meg a következő lépéssel: [konfigurálása RBS](#configure-rbs).

