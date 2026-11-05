# Payment MVP — Realtime Database Rules

Branch: `payment-system-work`

This is the complete proposed Rules JSON based on the Rules pasted in the project conversation, with additions for `paymentRequests` and `paymentDashboards`.

**Do not paste these Rules until reviewed in Firebase Console.** They preserve the previous public read paths and permit unauthenticated creation of pending payment requests only. Existing pending requests allow UTR/submission timestamp updates while core payment fields remain immutable; Admin can review and update statuses. This is a proposed rules file and must be tested in Firebase Rules Simulator before applying.

Important MVP limitation: public `questions`, `chapters`, and Google Drive PDF URLs remain public as they were before. Dashboard approval is a workflow control, not full content protection.

```json
{
  "rules": {
    ".read": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true",
    ".write": false,
    "admins": {
      "$uid": {
        ".read": "auth != null && auth.uid == $uid",
        ".write": "auth != null && auth.uid == $uid && $uid == 'swWYszc7mMWQVJrWFFmiNBIV3al2' && !data.exists()",
        ".validate": "newData.hasChildren(['role', 'active']) && newData.child('role').val() == 'admin' && newData.child('active').isBoolean()"
      }
    },
    "videos": {
      ".read": true,
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "chapters": {
      ".read": true,
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "quizzes": {
      ".read": true,
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "questions": {
      ".read": true,
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "students": {
      ".read": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true",
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "notices": {
      ".read": true,
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "payments": {
      ".read": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true",
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "paymentRequests": {
      ".read": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true",
      "$requestId": {
        ".write": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || (auth == null && !data.exists() && newData.child('status').val() == 'pending' && newData.hasChildren(['studentName','phone','studentId','productType','productId','productName','amount','status','requestSecret','createdAt'])) || (auth == null && data.exists() && data.child('status').val() == 'pending' && newData.child('status').val() == 'pending' && newData.child('requestSecret').val() == data.child('requestSecret').val())",
        ".validate": "newData.hasChildren(['studentName','phone','studentId','productType','productId','productName','amount','status','requestSecret','createdAt'])",
        "studentName": {
          ".validate": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || !data.exists() || newData.val() == data.val()"
        },
        "phone": {
          ".validate": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || !data.exists() || newData.val() == data.val()"
        },
        "studentId": {
          ".validate": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || !data.exists() || newData.val() == data.val()"
        },
        "productType": {
          ".validate": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || !data.exists() || newData.val() == data.val()"
        },
        "productId": {
          ".validate": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || !data.exists() || newData.val() == data.val()"
        },
        "productName": {
          ".validate": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || !data.exists() || newData.val() == data.val()"
        },
        "amount": {
          ".validate": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || !data.exists() || newData.val() == data.val()"
        },
        "status": {
          ".validate": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || (!data.exists() && newData.val() == 'pending') || newData.val() == data.val()"
        },
        "requestSecret": {
          ".validate": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || !data.exists() || newData.val() == data.val()"
        },
        "utr": {
          ".validate": "newData.isString() && newData.val().length <= 40"
        },
        "submittedAt": {
          ".validate": "newData.isNumber()"
        },
        "createdAt": {
          ".validate": "newData.isNumber()"
        },
        "reviewedAt": {
          ".validate": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
        },
        "reviewedBy": {
          ".validate": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
        },
        "rejectionReason": {
          ".validate": "newData.isString() && newData.val().length <= 240"
        }
      }
    },
    "paymentDashboards": {
      "$token": {
        ".read": true,
        ".write": "(auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true) || !data.exists()"
      }
    },
    "results": {
      ".read": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true",
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "users": {
      ".read": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true",
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "exams": {
      ".read": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true",
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "answers": {
      ".read": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true",
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "certificates": {
      ".read": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true",
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "pdfs": {
      ".read": true,
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "settings": {
      ".read": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true",
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "siteContent": {
      ".read": true,
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "paymentPrices": {
      ".read": true,
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    },
    "paymentConfig": {
      ".read": true,
      ".write": "auth != null && root.child('admins').child(auth.uid).child('role').val() == 'admin' && root.child('admins').child(auth.uid).child('active').val() == true"
    }
  }
}
```
