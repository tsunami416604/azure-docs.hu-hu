
1. Az a **hibrid kapcsolatok** panelen, kattintson az imént a hibrid kapcsolat létrejött, majd kattintson **figyelő telepítő**.
   
    ![Kattintson a figyelő beállítás](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
2. A **hibrid kapcsolat tulajdonságai** panel nyílik meg. A **helyszíni Hybrid Connection Manager**, válassza a **manuális letöltés és beállítás**, mentse a letöltött HybridConnectionManager.msi csomagot, és másolja az átjáró kapcsolati karakterlánca.
   
    ![Kattintson ide](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
3. Rendszergazdai jogú parancssort írja be a telepítő elindításához a következő parancsot:
   
        start HybridConnectionManager.msi
4. A telepítő futtatását követően kattintson **most nem**, majd tallózással keresse meg a %ProgramFiles%\Microsoft\HybridConnectionManager mappát, HCMConfigWizard.exe futtatni, és kattintson a **Igen** a a **felhasználói fiók Vezérlő** párbeszédpanel.
5. Illessze be a hibrid korábban kimásolt kapcsolati karakterláncot, és kattintson a **OK**. 
   
    ![Telepítése](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
6. A telepítés befejeztével kattintson **Bezárás**.
   
    ![Kattintson a Bezárás gombra](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
   
    Az a **hibrid kapcsolatok** panelen a **állapot** most az oszlop látható **csatlakoztatva**. 
   
    ![Csatlakoztatott állapot](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)

