

新的 Twitter v1.1 API 會要求您的應用程式先進行驗證再存取資源。 首先，您需要使用 OAuth 2.0 取得所需的認證以要求存取權。 接著針對您的行動服務，將它們安全地儲存在應用程式設定中。

1. 如果尚未執行此操作，請完成<a href="../articles/mobile-services/mobile-services-how-to-register-twitter-authentication.md/" target="_blank">在行動服務中註冊您的應用程式以進行 Twitter 登入</a>主題中的步驟。 
  
    Twitter 會產生需要的認證，讓您可以存取 Twitter v1.1 API。 您可以從 Twitter Developers 網站取得這些認證。 

2. 瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter Developers</a> 網站，使用您的 Twitter 帳戶認證登入，並選取您的 Twitter 應用程式。

3. 在 **金鑰和存取權杖** 應用程式索引標籤中，記下的下列值 ︰

    + **消費者金鑰**
    + **消費者密碼**
    + **存取權杖**
    + **存取權杖密碼**

4. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **行動電話服務**, ，然後按一下您的行動服務。

5. 按一下 [ **識別** 索引標籤上，輸入 **消費者金鑰** 和 **消費者密鑰** 從 Twitter，然後按一下 [取得的值 **儲存**。 

    ![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. 按一下 [ **設定** 索引標籤上，向下捲動至 **應用程式設定** 輸入 **名稱** 和 **值** 您是從 Twitter 網站取得下列各項的配對，然後按一下 [ **儲存**。

    + `TWITTER_ACCESS_TOKEN`
    + `TWITTER_ACCESS_TOKEN_SECRET`

    ![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

    這會在應用程式設定中儲存 Twitter 存取權杖。 要在上的消費者認證 **識別** ] 索引標籤，存取認證也會儲存在應用程式設定中加密，您可以在伺服器指令碼中存取它們，而不將它們硬式編碼指令碼檔案中。 如需詳細資訊，請參閱 [App settings]。

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Register your apps for Twitter login with Mobile Services]: ../articles/mobile-services/mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/azure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

