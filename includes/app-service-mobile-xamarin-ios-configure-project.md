####在 Xamarin Studio 中設定 iOS 專案

1. 在 Xamarin.Studio 中，開啟 **Info.plist**, ，並更新 **Bundle Identifier** 配套識別碼為您稍早建立的新的應用程式識別碼。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)

2. 向下捲動至 **背景模式** 並檢查 **啟用背景模式** 方塊和 **遠端通知** 方塊。 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)

3. 按兩下以開啟 [方案] 面板中的專案 **專案選項**。

4.  選擇 **iOS 套件組合簽署** 下 **建置**, ，然後選取對應 **識別** 和 **佈建設定檔** 您設定此專案。 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

    這將確保專案使用新的設定檔進行程式碼簽署。 如需官方 Xamarin 裝置佈建文件，請參閱 [Xamarin Device Provisioning]。

####在 Visual Studio 中設定 iOS 專案

1. 在 Visual Studio 中，以滑鼠右鍵按一下專案，以及 [ **屬性**。

2. 在 [屬性頁中，按一下 [ **iOS 應用程式** 索引標籤，然後更新 **識別碼** 您稍早建立的識別碼。

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)

3. 在 **iOS 套件組合簽署** 索引標籤上，選取對應 **識別** 和 **佈建設定檔** 您設定此專案。 

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    這將確保專案使用新的設定檔進行程式碼簽署。 如需官方 Xamarin 裝置佈建文件，請參閱 [Xamarin Device Provisioning]。

4. 按兩下以開啟它，然後啟用 Info.plist **RemoteNotifications** 背景模式下。 



[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/

