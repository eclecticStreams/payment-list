# payment-list

<!--
==========================================================
FIRESTORE SECURITY RULES
Paste into: Firebase Console → Firestore → Rules → Publish
==========================================================

rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    function isSignedIn() {
      return request.auth != null;
    }
    function isAdmin() {
      return isSignedIn() &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }

    // Students: public read, any editor can tick, only admin can add/delete
    match /students/{id} {
      allow read: if true;
      allow update: if isSignedIn();
      allow create, delete: if isAdmin();
    }

    // Attendance legacy doc (not used in v4 but kept for safety)
    match /attendance/{doc} {
      allow read: if true;
      allow write: if isSignedIn();
    }

    // Audit: any signed-in user can write, only admin can read
    match /audit/{doc} {
      allow read: if isAdmin();
      allow create: if isSignedIn();
    }

    // Presence: users can only update their own record
    match /presence/{uid} {
      allow read: if isSignedIn();
      allow write: if isSignedIn() && request.auth.uid == uid;
    }

    // Users collection: admin-only write, any signed-in user can read
    match /users/{uid} {
      allow read: if isSignedIn();
      allow write: if isAdmin();
    }
  }
}

==========================================================
-->
