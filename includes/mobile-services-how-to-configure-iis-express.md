
1. 如果行動服務目前正在 IIS Express 中執行，請確實加以停止。 以滑鼠右鍵按一下 IIS Express 系統匣圖示，按一下 [ **停止** 行動服務。

    ![](./media/mobile-services-how-to-configure-iis-express/iis-express-tray-stop-site.png)


2. 在命令提示字元視窗中，執行 **ipconfig** 命令，以找出有效的本機 IP 位址為您的工作站。

    ![](./media/mobile-services-how-to-configure-iis-express/ipconfig.png)


3. 在 Visual Studio 中，開啟 IIS Express 的 applicationhost.config 檔案。 此檔案位於使用者設定檔目錄的下列子目錄中。

        C:\Users\<your profile name>\Documents\IISExpress\config\applicationhost.config

4. 設定 IIS Express 以允許對服務的遠端連線要求。 若要執行此動作，請在 applicationhost.config 檔案中找到行動服務的網站元素，然後使用您先前記下的 IP 位址，為連接埠增加新的 `binding` 元素。 接著，請儲存 applicationhost.config 檔案。 

    更新的網站元素應會如下所示：

        <site name="todolist_Service(1)" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="C:\Archive\GetStartedDataWP8\C#\todolist_Service" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:58203:localhost" />
                <binding protocol="http" bindingInformation="*:58203:192.168.137.72" />
            </bindings>
        </site>

5. 開啟 Windows 防火牆主控台，然後建立新的連接埠規則，以允許對連接埠的連線。 如需有關如何建立新的 Windows 防火牆連接埠規則的詳細資訊，請參閱 [如何加入新的 Windows 防火牆連接埠規則]。

    >[AZURE.NOTE] 如果您的測試機器已加入網域，可能會受到網域原則控制防火牆例外。 在此情況下，您必須與網域管理員連絡，以了解機器連接埠的例外。

    現在，您應已完成對代管行動服務的 IIS Express 進行測試所需的設定。 

    >[AZURE.NOTE] 一旦您完成對服務的本機測試，您應該刪除先前建立的 Windows 防火牆規則。 


<!-- URLs. -->
[How to add a new Windows Firewall port rule]:  http://go.microsoft.com/fwlink/?LinkId=392240

