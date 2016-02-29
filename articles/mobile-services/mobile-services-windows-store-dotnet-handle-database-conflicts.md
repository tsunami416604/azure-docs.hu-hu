<properties
    pageTitle="使用開放式並行存取處理資料庫寫入衝突 (Windows 市集) | Microsoft Azure"
    description="了解如何處理伺服器與 Windows 市集應用程式之間的資料庫寫入衝突。"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/05/2015"
    ms.author="wesmc"/>

# 處理資料庫寫入衝突

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;




##概觀

本教學課程可協助您深入了解如何處理在兩個或更多用戶端寫入至 Windows 市集應用程式中的相同資料庫記錄時所發生的衝突。 在部分案例中，兩個或多個用戶端可能會同時對相同項目寫入變更。 在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。 Azure 行動服務可支援偵測及解決這些衝突的作業。 本主題將逐步引導您完成下列步驟，讓您處理伺服器上與應用程式中的資料庫寫入衝突。

在本教學課程中，您會在快速入門應用程式中新增功能，以處理更新 TodoItem 資料庫時所發生的爭用情況。


##必要條件

本教學課程需要下列各項

+ Microsoft Visual Studio 2013 或更新版本。
+ 本教學課程會以行動服務快速入門為基礎。 在開始本教學課程之前，您必須先完成 [開始使用行動服務]。
+ [Azure 帳戶]
+ Azure 行動服務 NuGet 封裝 1.1.0 或更新版本。 若要取得最新版本，請遵循下列步驟：
    1. 在 Visual Studio 中，開啟專案，並以滑鼠右鍵按一下 [方案總管] 中的專案，然後按一下 [ **管理 Nuget 封裝**。

        ![][19]

    2. 展開 **線上** 按一下 **Microsoft and.NET**。 在搜尋文字方塊中輸入 **Azure 行動服務**。 按一下 [ **安裝** 上 **Azure 行動服務** NuGet 封裝。

        ![][20]




##更新應用程式以允許更新

在本節中您將會更新 TodoList 使用者介面，使 ListBox 控制項中各個項目的文字能夠進行更新。 ListBox 將包含資料庫資料表中各個項目的 CheckBox 和 TextBox 控制項。 您將可更新 TodoItem 的文字欄位。 應用程式會處理來自該 TextBox 的 `LostFocus` 事件，以更新資料庫中的項目。


1. 在 Visual Studio 中，開啟您在下載的 TodoList 專案 [開始使用行動服務] 教學課程。
2. 在 Visual Studio 的 [方案總管] 中開啟 MainPage.xaml，將 `ListView` 定義以下方顯示的 `ListView` 取代，然後儲存變更。

        <ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Horizontal">
                        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
                        <TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
                    </StackPanel>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>


4. 在 Visual Studio 方案總管中，開啟共用專案中的 MainPage.cs。 將事件處理常式新增至 TextBox `LostFocus` 事件的 MainPage，如下所示。


        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. 在共用專案的 MainPage.cs 中，為事件處理常式中參考的 MainPage `UpdateToDoItem()` 方法加入定義，如下所示。

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
            }
        }

現在，當 TextBox 失去焦點時，應用程式會將每個項目的文字變更重新寫入至資料庫。

##在應用程式中啟用衝突偵測

在部分案例中，兩個或多個用戶端可能會同時對相同項目寫入變更。 在沒有偵測到任何衝突的情況下，最後寫入將覆寫任何先前的更新，即使這不是您想要的結果。 [開放式並行存取控制] 會假設每筆交易可以認可，因此不使用任何資源鎖定。 在認可交易之前，開放式並行存取控制項會驗證沒有其他交易已修改此資料。 如果資料已修改，則會復原認可的交易。 Azure 行動服務支援開放式並行存取控制項，方法是使用新增至每個資料表的 `__version` 系統屬性資料欄來追蹤對每個項目的變更。 在本節中，我們將使應用程式能夠透過 `__version` 系統屬性偵測這些寫入衝突。 在嘗試更新期間，如果記錄自前次查詢後有所變更，系統將會以 `MobileServicePreconditionFailedException` 通知應用程式。 此時，應用程式將可選擇是要認可它對資料庫的變更，還是保留資料庫的最後變更。 如需有關系統屬性適用於行動服務，請參閱 [系統內容]。

1. 開啟共用專案中的 TodoItem.cs，以下列程式碼更新 `TodoItem` 類別定義，以加入可用來支援寫入衝突偵測的 `__version` 系統屬性。

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
            [JsonProperty(PropertyName = "__version")]
            public string Version { set; get; }
        }

    > [AZURE.NOTE] 使用不具型別的的資料表時，啟用開放式並行存取是將資料表 SystemProperties 中新增 Version 旗標。
    >
    >`````
    //Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
`````


2. By adding the `Version` property to the `TodoItem` class, the application will be notified with a `MobileServicePreconditionFailedException` exception during an update if the record has changed since the last query. This exception includes the latest version of the item from the server. In MainPage.cs for the shared project, add the following code to handle the exception in the `UpdateToDoItem()` method.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException)
                {
                    //Conflict detected, the item has changed since the last query
                    //Resolve the conflict between the local and server item
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                }
                else
                {
                    await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                }
            }
        }


3. In MainPage.cs, add the definition for the `ResolveConflict()` method referenced in `UpdateToDoItem()`. Notice that in order to resolve the conflict, you set the local item's version to the updated version from the server before committing the user's decision. Otherwise, you will continually encounter the conflict.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
        {
            //Ask user to choose the resolution between versions
            MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
                                                        serverItem.Text, localItem.Text),
                                                        "CONFLICT DETECTED - Select a resolution:");
            UICommand localBtn = new UICommand("Commit Local Text");
            UICommand ServerBtn = new UICommand("Leave Server Text");
            msgDialog.Commands.Add(localBtn);
            msgDialog.Commands.Add(ServerBtn);
            localBtn.Invoked = async (IUICommand command) =>
            {
                // To resolve the conflict, update the version of the
                // item being committed. Otherwise, you will keep
                // catching a MobileServicePreConditionFailedException.
                localItem.Version = serverItem.Version;
                // Updating recursively here just in case another
                // change happened while the user was making a decision
                await UpdateToDoItem(localItem);
            };
            ServerBtn.Invoked = async (IUICommand command) =>
            {
                RefreshTodoItems();
            };
            await msgDialog.ShowAsync();
        }



##Test database write conflicts in the application

In this section you will build a Windows Store app package to install the app on a second machine or virtual machine. Then you will run the app on both machines generating a write conflict to test the code. Both instances of the app will attempt to update the same item's `text` property requiring the user to resolve the conflict.


1. Create a Windows Store app package to install on second machine or virtual machine. To do this, click **Project**->**Store**->**Create App Packages** in Visual Studio.

    ![][0]

2. On the Create Your Packages screen, click **No** as this package will not be uploaded to the Windows Store. Then click **Next**.

    ![][1]

3. On the Select and Configure Packages screen, accept the defaults and click **Create**.

    ![][10]

4. On the Package Creation Completed screen, click the **Output location** link to open the package location.

    ![][11]

5. Copy the package folder, "todolist_1.0.0.0_AnyCPU_Debug_Test", to the second machine. On that machine, open the package folder and right click on the **Add-AppDevPackage.ps1** PowerShell script and click **Run with PowerShell** as shown below. Follow the prompts to install the app.

    ![][12]

5. Run instance 1 of the app in Visual Studio by clicking **Debug**->**Start Debugging**. On the Start screen of the second machine, click the down arrow to see "Apps by name". Then click the **todolist** app to run instance 2 of the app.

    App Instance 1
    ![][2]

    App Instance 2
    ![][2]


6. In instance 1 of the app, update the text of the last item to **Test Write 1**, then click another text box so that the `LostFocus` event handler updates the database. The screenshot below shows an example.

    App Instance 1
    ![][3]

    App Instance 2
    ![][2]

7. At this point the corresponding item in instance 2 of the app has an old version of the item. In that instance of the app, enter **Test Write 2** for the `text` property. Then click another text box so the `LostFocus` event handler attempts to update the database with the old `_version` property.

    App Instance 1
    ![][4]

    App Instance 2
    ![][5]

8. Since the `__version` value used with the update attempt didn't match the server `__version` value, the Mobile Services SDK throws a `MobileServicePreconditionFailedException` allowing the app to resolve this conflict. To resolve the conflict, you can click **Commit Local Text** to commit the values from instance 2. Alternatively, click **Leave Server Text** to discard the values in instance 2, leaving the values from instance 1 of the app committed.

    App Instance 1
    ![][4]

    App Instance 2
    ![][6]



##Automatically handling conflict resolution in server scripts

You can detect and resolve write conflicts in server scripts. This is a good idea when you can use scripted logic instead of user interaction to resolve the conflict. In this section, you will add a server side script to the TodoItem table for the application. The logic this script will use to resolve conflicts is as follows:

+  If the TodoItem's ` complete` field is set to true, then it is considered completed and `text` can no longer be changed.
+  If the TodoItem's ` complete` field is still false, then attempts to update `text` will be comitted.

The following steps walk you through adding the server update script and testing it.

1. Log into the [Azure classic portal], click **Mobile Services**, and then click your app.

    ![][7]

2. Click the **Data** tab, then click the **TodoItem** table.

    ![][8]

3. Click **Script**, then select the **Update** operation.

    ![][9]

4. Replace the existing script with the following function, and then click **Save**.

        function update(item, user, request) {
            request.execute({
                conflict: function (serverRecord) {
                    // Only committing changes if the item is not completed.
                    if (serverRecord.complete === false) {
                        //write the updated item to the table
                        request.execute();
                    }
                    else
                    {
                        request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
                    }
                }
            });
        }
5. Run the **todolist** app on both machines. Change the TodoItem `text` for the last item in instance 2. Then click another text box so the `LostFocus` event handler updates the database.

    App Instance 1
    ![][4]

    App Instance 2
    ![][5]

6. In instance 1 of the app, enter a different value for the last text property. Then click another text box so the `LostFocus` event handler attempts to update the database with an incorrect `__version` property.

    App Instance 1
    ![][13]

    App Instance 2
    ![][14]

7. Notice that no exception was encountered in the app since the server script resolved the conflict allowing the update since the item is not marked complete. To see that the update was truly successful, click **Refresh** in instance 2 to re-query the database.

    App Instance 1
    ![][15]

    App Instance 2
    ![][15]

8. In instance 1, click the check box to complete the last Todo item.

    App Instance 1
    ![][16]

    App Instance 2
    ![][15]

9. In instance 2, try to update the last TodoItem's text and trigger the `LostFocus` event. In response to the conflict, the script resolved it by refusing the update because the item was already completed.

    App Instance 1
    ![][17]

    App Instance 2
    ![][18]

##Next steps

This tutorial demonstrated how to enable a Windows Store app to handle write conflicts when working with data in Mobile Services. Next, consider completing one of the following Windows Store tutorials:

* [Add authentication to your app]
  <br/>Learn how to authenticate users of your app.

* [Add push notifications to your app]
  <br/>Learn how to send a very basic push notification to your app with Mobile Services.



<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[Optimistic Concurrency Control]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Azure Account]: http://www.windowsazure.com/pricing/free-trial/
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Add authentication to your app]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Add push notifications to your app]: /develop/mobile/tutorials/get-started-with-push-dotnet

[Azure classic portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[System Properties]: http://go.microsoft.com/fwlink/?LinkId=331143

