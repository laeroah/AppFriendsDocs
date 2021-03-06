## Open Channels
Open channel is a public chat, where all of your users can participate. It can handle thousands of users in one channel. ex) Twitch-style public chat.
You can create open channel from your AppFriends web control panel. After a channel is created, all of your users can see all the channels with `GET /channels` API. A user can only be inside one channel at a time.

List all channels:
```swift
AFDialog.getChannels { (dialogs, error) in
}
```

The easiest way to use open channel is by using `HCChannelChatViewController`.
```swift
let channelChatVC = HCChannelChatViewController(dialogID: dialogID)
```

## Private Dialogs

### Listing All Private Dialogs
AppFriends SDK remembers all the private dialogs and you can get the list of private dialogs by:
```swift
AFDialog.getDialogs { (dialogs, error) in
    // this returns an array of all the private dialogs of type AFDialog
}
```
### Group Dialog
A group dialog is a private dialog which is only visible to users in it. You can add more users or remove users from the dialog. To start a group dialog, you need to first create a group dialog:
```swift
AFDialog.createGroupDialog(dialogID: id, members: users, customData: data, pushData: pushData, title: dialogTitle, completion: { (id, error) in
      if error != nil {
        self.showErrorWithMessage(error?.localizedDescription)
      } else if let dialogID = id {
        let dialogVC = HCDialogChatViewController(dialogID: dialogID)
        self.navigationController?.pushViewController(dialogVC, animated: true)
      }
})
```
Please note that you can provide an id to this dialog. It is optional, and if you do not provide an id, AppFriends will assign an unique id to this dialog. This is a good way for you to bind the dialog with certain feature or part of your app.

### Individual Dialog
An individual dialog is a private dialog between two users. You cannot add more users to this dialog. To start an individual dialog:
```swift
AFDialog.createIndividualDialog(withUser: userID, completion: { (dialogID, error) in
    if error != nil {
      self.showErrorWithMessage(error?.localizedDescription)
    } else if let dialogID = id {
      let dialogVC = HCDialogChatViewController(dialogID: dialogID)
      self.navigationController?.pushViewController(dialogVC, animated: true)
    }
})
```

## Mute/Unmute
You can mute/unmute a dialog by:
```swift
// dialog is an AFDialog instance
dialog.mute()
dialog.unmute()
```

## Badge/Unread Messages
The total number of unread messages by the current user can be found by:
```swift
let totalUnreadCount = AFDialog.totalUnreadMessageCount()
```
For each dialog, the unread message can be found by using `AFDialog: unreadMessageCount`.
When unread message count has updated, you can subscribe to `AFEvent`. You can monitor the notification and update the badge on the app icon. eg.
```swift
// subscribe to AFEvent
AFEvent.subscribe(subscriber: self)

// handle notification
func emitEvent(_ event: AFEvent) {
    if event.name == .eventTotalUnreadCountChange {
        // read badge number from AFDialog.totalUnreadMessageCount()
    }
}
```

## Sending Messages
Sending text:
```swift
// dialog is an AFDialog instance
dialog.sendText(text: text, requireReceipt: true, mentionedUsers: mentionedIDs, completion: { (error) in

    if error == nil {
      self.didSendTextMessage()
    }
})
```

Sending an image:
```swift
// dialog is an AFDialog instance
dialog.sendImage(image: image, requireReceipt: false, completion: { (error) in

}, progress: { (percentage) in

})
```

Sending a video:
```swift
// dialog is an AFDialog instance
dialog.sendVideo(videoData: videoData, requireReceipt: self._requireReceipt, completion: { (error) in

}, progress: { (percentage) in

})
```

Sending a gif
```swift
// dialog is an AFDialog instance
dialog.sendGif(url: url, requireReceipt: false, completion: { (error) in

})
```

## Mentioning Users
You can mention other users by referencing their IDs in the message you compose.
```swift
// see mentionedUsers parameter
// it's an array of mentioned users' IDs
dialog.sendText(text: text, requireReceipt: true, mentionedUsers: mentionedIDs, completion: { (error) in

    if error == nil {
      self.didSendTextMessage()
    }
})
```

## Message Receipts
You can check the receipts of a message by using `AFMessage` class:
```swift
// message is an AFMessage object
message.getReceipts { (receivedUserIDs, readUserIDs, error) in
    // receivedUserIDs contains id's of the users who received message
    // readUserIDs contains id's of the users who have read the message
}
```
Sending Receipts:
If you use the chat view provided in AppFriendsUI SDK, you do not need to manually send the receipts. The UI SDK automatically handles it. If you wish to send receipts yourself, you can call these API on a `AFMessage` object:
```swift
// message is an AFMessage object
message.markAsRead()      // post read receipt
message.markAsReceived()  // post received receipt
```
