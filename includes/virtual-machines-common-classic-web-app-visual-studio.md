

Egy webalkalmazás-projekt létrehozásakor az Azure-hoz, az Azure-beli virtuális gép építhető ki. Ezután konfigurálja a virtuális gépet egy másik szoftver-, vagy használja a virtuális gép diagnosztikai és hibakeresési célokra.

Hozzon létre egy virtuális gépet, egy webalkalmazás létrehozásakor, kövesse az alábbi lépéseket:

1. A Visual Studióban kattintson **fájl** > **új** > **projekt** > **webes**, és válassza a **ASP.NET-alkalmazás** (alatt a **Visual C#** vagy **Visual Basic** csomópontok).
2. Az a **új ASP.NET-projekt** párbeszédpanelen válassza ki a kívánt webalkalmazást, és az Azure a párbeszédpanel (a jobb alsó sarokban) szakaszban győződjön meg arról, hogy a **üzemeltetés a felhőben** jelölőnégyzet kiválasztott (ezt a jelölőnégyzetet feliratú **létre távoli erőforrásokat** az egyes telepítések).
   
    ![][0]
3. Ebben a példában a Microsoft Azure-alatti legördülő listában válassza ki a **virtuális gép (v1)**, majd kattintson a **OK** gombra.
4. Ha az kéri, jelentkezzen be Azure. A **virtuális gép létrehozása** párbeszédpanel jelenik meg.
   
    ![][2]
5. Az a **DNS-név** adjon meg egy nevet a virtuális gép. A DNS-neve az Azure-ban egyedinek kell lennie. Ha a megadott név nem érhető el, piros felkiáltójel jelenik meg.
6. Az a **kép** menüben válassza ki a keresett rendszerképet, a virtuális gép alapuljon. A standard szintű Azure-beli virtuálisgép-lemezképek, vagy a rendszerképet, amelyet az Azure-bA feltöltött közül választhat.
7. Hagyja a **engedélyezi az IIS és a Web Deploy** jelölőnégyzet be van jelölve, kivéve, ha azt tervezi, hogy egy másik webkiszolgálót. Közzététel a Visual Studióból, ha letiltja a Web Deploy tudni fogunk. Az IIS és a Web Deploy a csomagolt Windows Server-rendszerképeket, akár saját lemezképek alapján is valamelyik adhat hozzá.
8. Az a **mérete** menüben válassza ki a virtuális gép méretét.
9. Adja meg a virtuális gép bejelentkezési hitelesítő adataival. Jegyezze fel őket, mert szükség lesz rájuk, hozzáférhet a géphez a távoli asztalon keresztül.
10. Az a **hely** menüben válassza ki a régiót, a virtuális gép működtetéséhez.
11. Kattintson a **OK** gombra kattintva indítsa el a virtuális gépet hoz létre. Követheti a művelet előrehaladását a **kimeneti** ablak.
    
    ![][3]
12. Ha a virtuális gép ki van építve, közzétett parancsfájlok jönnek létre egy **PublishScripts** a megoldás csomópontjára. A közzétett parancsfájl fut, és kiosztja a virtuális gép az Azure-ban. A **kimeneti** ablak állapotát jeleníti meg. A szkript a virtuális gép beállításához a következő műveleteket hajtja végre:
    
    * A virtuális gépet hoz létre, ha még nem létezik.
    * Létrehoz egy tárfiókot kezdődő nevű `devtest`, de csak akkor, ha még nem található ilyen storage-fiók a megadott régióban.
    * Egy felhőalapú szolgáltatás létrehozása a virtuális gép tárolójaként, és a webes alkalmazás egy webes szerepkört hoz létre.
    * A Web Deploy konfigurálja a virtuális gépen.
    * Konfigurálja az IIS és ASP.NET a virtuális gépen.
    
    ![][4]
13. (Nem kötelező) Az új virtuális gép csatlakozhat. A **Server Explorer**, bontsa ki a **virtuális gépek** csomópontot, válassza a csomópontot a létrehozott virtuális gép, és a helyi menü, válassza a **csatlakoztatása a távoli asztal**. Másik lehetőségként a **Cloud Explorer** választhat **Megnyitás a portálon** a helyi menüben, és csatlakozzon a virtuális géphez van.
    
    ![][5]

## <a name="next-steps"></a>További lépések
Ha szeretné testre szabhatja a közzétett parancsfájlok hozott létre, olvassa el részletesebb információt, [Windows PowerShell parancsfájlok használata a fejlesztési és tesztelési környezetek a közzététel](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png
