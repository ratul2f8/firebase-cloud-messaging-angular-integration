Add proper configuration in `firebase-messaging-sw.js` and `environment.ts` file. Open up the developer console and you will get the registration token. Than, you can use the token to send notification using firebase admin-sdk like so.

```
@Injectable()
export class FirebaseMessageService {
  constructor() {
    if (!firebaseAdmin.apps.length) {
      firebaseAdmin.initializeApp({
        credential: firebaseAdmin.credential.cert(
          <JSON cert generated from firebase console> as ServiceAccount,
        ),
        storageBucket: 'your_storage_buckst',
      });
    } else {
      firebaseAdmin.app();
    }
  }
  public async sendMessage(
    registrationToken: string,
    payload: MessagingPayload,
  ) {
    try {
      await firebaseAdmin.messaging().sendMulticast({
        tokens: [registrationToken],
        notification: {
          title: 'Basic Notification',
          body: 'Yep push notification is working',
          imageUrl:
            'https://upload.wikimedia.org/wikipedia/commons/thumb/c/cf/Angular_full_color_logo.svg/2048px-Angular_full_color_logo.svg.png',
        },
      });
    } catch (e) {
      console.error('Firebase cloud message error : ', e);
    }
  }
}
```