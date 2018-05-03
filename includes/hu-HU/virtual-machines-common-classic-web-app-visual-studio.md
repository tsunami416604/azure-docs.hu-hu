

Egy webalkalmazás projekthez az Azure-ba való létrehozásakor megadhat egy virtuális gép az Azure-ban. Ezután konfigurálja a virtuális gép egy másik szoftver, vagy a virtuális gép hibakereséshez vagy diagnosztikai célokra használja.

Egy webalkalmazás létrehozásakor egy virtuális gép létrehozásához kövesse az alábbi lépéseket:

1. A Visual Studióban kattintson **fájl** > **új** > **projekt** > **webes**, és válassza a **ASP.NET Web Application** (alatt a **Visual C#** vagy **Visual Basic** csomópontok).
2. Az a **új ASP.NET projekt** párbeszédpanelen válassza ki a kívánt webes alkalmazás, és a párbeszédpanel (a jobb alsó sarokban) Azure szakaszában, győződjön meg arról, hogy a **a felhőben lévő gazdagéphez** jelölőnégyzet kijelölt (Ez a jelölőnégyzet be van jelölve **távoli erőforrások létrehozása** néhány telepítések esetén).
   
    ![][0]
3. Ebben a példában a Microsoft Azure-ban a legördülő listában válasszon **virtuális gép (v1)**, majd kattintson a **OK** gomb.
4. Ha a számítógép, jelentkezzen be Azure. A **virtuális gép létrehozása** párbeszédpanel jelenik meg.
   
    ![][2]
5. Az a **DNS-név** adjon meg egy nevet a virtuális gép. A DNS-neve az Azure-ban egyedinek kell lennie. Ha a megadott név nem érhető el, egy piros felkiáltójel jelenik meg.
6. Az a **kép** menüben válassza ki a virtuális gép alapjául kívánt lemezképet. A szabványos Azure virtuálisgép-lemezképeket vagy a kép, amelyet az Azure-bA feltöltött választhat.
7. Hagyja a **engedélyezése az IIS és a Web Deploy** jelölőnégyzet be van jelölve, kivéve, ha egy másik webkiszolgálón telepíteni szeretné. Ön nem fog tudni közzé a Visual Studio eszközből, ha letiltja a Web Deploy keretrendszert. Az IIS és a Web Deploy adhat hozzá a csomagolt Windows Server-lemezképek, beleértve a saját egyéni lemezképek egyikének sem.
8. Az a **mérete** menüben válassza ki a virtuális gép méretét.
9. Adja meg a bejelentkezési hitelesítő adatait a virtuális gép számára. Jegyezze fel, mert szüksége, hogy hozzáférjenek a számítógép távoli asztalon keresztül.
10. Az a **hely** menüben válassza ki a régiót, a virtuális gép működtetéséhez.
11. Kattintson a **OK** gombra kattintva indítsa el a virtuális gép létrehozása. A műveletnek az előrehaladását követheti a **kimeneti** ablak.
    
    ![][3]
12. Amikor a virtuális gép ki van építve, közzétett parancsfájlok jön létre egy **PublishScripts** csomópont a megoldásban. A közzétett parancsfájl fut, és az Azure virtuális gép kiépítését. A **kimeneti** ablak állapotát jeleníti meg. A parancsfájl a virtuális gép beállítása a következő műveleteket hajtja végre:
    
    * A virtuális gépet hoz létre, ha még nem létezik.
    * Tárfiók létrehozása kezdődő nevű `devtest`, de csak akkor, ha még nem ilyen a storage-fiók a megadott régión belül.
    * A virtuális gép elhelyezése egy felhőalapú szolgáltatás létrehozása, és létrehoz egy webes szerepkör, a webes alkalmazás.
    * A Web Deploy konfigurálja a virtuális gépen.
    * Konfigurálja az IIS és ASP.NET a virtuális gépen.
    
    ![][4]
13. (Választható) Kapcsolódás az új virtuális gépet. A **Server Explorer**, bontsa ki a **virtuális gépek** csomópont, a csomópont a létrehozott virtuális gép, és a helyi menü választhat, **csatlakozás a távoli asztal**. Alternatív megoldásként a **Cloud Explorer** dönthet úgy **nyissa meg a portál** a helyi menüben, és csatlakozzon a virtuális gép van.
    
    ![][5]

## <a name="next-steps"></a>Következő lépések
Ha szeretné testre szabni a közzétett parancsfájlok, olvassa el részletesebb információt: [Windows PowerShell parancsfájlok használata a közzététel a fejlesztési és tesztkörnyezetek](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png
