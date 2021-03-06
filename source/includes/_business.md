# Business

> An example invitation object:

```
{
  "base_url": "http://todoist.com",
  "business_account_id": 2,
  "email": "z@example.com",
  "id": 13,
  "inviter_id": 1,
  "message": "Welcome to our company business account",
  "secret": "0692805f97d9c091debaba52d18f5696",
  "user_id": 1
}
```

The set of functions and Sync API commands to manage Todoist Business accounts. All these functionality is only available for Business account admins.

In order to become a business account admin, you should create a new account from [Todoist Business Free Trial](https://todoist.com/business/freeTrial), or to ask another business account admin to invite you, and to make you an admin.

User, who is also a business account admin has the attribute `is_biz_admin`, set to `true`.

## Send invitation

> An example of sending an invitation:

```shell
$ curl https://todoist.com/API/v6/business/users/invite \
    -d token=0123456789abcdef0123456789abcdef01234567 \
    -d email_list='["spam@example.com","egg@example.com"]'
    -d message='Welcome'
[
  {
    "base_url": "http://todoist.com",
    "business_account_id": 2,
    "email": "spam@example.com",
    "id": 15,
    "inviter_id": 1,
    "message": "Welcome",
    "secret": "21d5a7fc4fa7e9d47a6642069d2af6ef",
    "user_id": 6
  },
  {
    "base_url": "http://todoist.com",
    "business_account_id": 2,
    "email": "egg@example.com",
    "id": 16,
    "inviter_id": 1,
    "message": "Welcome",
    "secret": "4e20a8b58abe07441c4c30fff34798ed",
    "user_id": 15
  }
]
```

```python
>>> import todoist
>>> api = todoist.TodoistAPI('0123456789abcdef0123456789abcdef01234567')
>>> api.business_users_invite(['spam@example.com','egg@example.com'])
[
  {
    'base_url': 'http://todoist.com',
    'business_account_id': 2,
    'email': 'spam@example.com',
    'id': 15,
    'inviter_id': 1,
    'message': 'Welcome',
    'secret': '21d5a7fc4fa7e9d47a6642069d2af6ef',
    'user_id': 6
  },
  {
    'base_url': 'http://todoist.com',
    'business_account_id': 2,
    'email': 'egg@example.com',
    'id': 16,
    'inviter_id': 1,
    'message': 'Welcome',
    'secret': '4e20a8b58abe07441c4c30fff34798ed',
    'user_id': 15
  }
]

```

This function allows you to send invitation to your business account. Every invitation object has unique id and secret code. Anyone who knows this information, can activate invitation and join your business account. Invitation is deactivated at the moment it's accepted or rejected by receiver, or deleted manually by sender.
 
Invitation objects are not created (quietly skipped), if the invitation recipient is an existing Todoist user and this user already belongs to a business account.

If an invitation for that recipient already exists and hasn't been activated yet, a new invitation object will not be created. Instead, an existing invitation will be re-sent and the corresponding invitation object will be returned back.

The return value is a list of invitation objects.

### Required arguments

Argument | Description
-------- | -----------
token *String* | User's access token
email_list *List of Strings* | The emails of users which will be invited

### Optional arguments

Argument | Description
-------- | -----------
message *String* | Additional text which will be included to invitation welcome message

## Accept invitation

> An example of accepting a business invitation with a direct call:

```shell
$ curl https://todoist.com/API/v6/business/users/accept_invitation \
    -d token=0123456789abcdef0123456789abcdef01234567 \
    -d id=1234 \
    -d secret=abcdefghijklmno
```

```python
>>> import todoist
>>> api = todoist.TodoistAPI('0123456789abcdef0123456789abcdef01234567')
>>> api.business_users_accept_invitation(1234, 'abcdefghijklmno')
```

> An example of accepting a business invitation with a Sync API command:

```shell
$ curl https://todoist.com/API/v6/sync \
    -d token=0123456789abcdef0123456789abcdef01234567 \
    -d commands='[{"type": "biz_accept_invitation", "uuid": "48538e47-7a9f-4f3d-927a-463ea997675e", "args": {"invitation_id": 1234,  "invitation_secret": "abcdefghijklmno"}}]'
{ ...
  "SyncStatus": {"48538e47-7a9f-4f3d-927a-463ea997675e": "ok"},
  ... }
```

```python
>>> import todoist
>>> api = todoist.TodoistAPI('0123456789abcdef0123456789abcdef01234567')
>>> api.biz_invitations.accept(1234, 'abcdefghijklmno')
>>> api.commit()
```

Accept an invitation from Todoist for Business.  There are currently 2 ways to accept an invitation, either with a direct call, or by using a Sync API command, as can be seen from the examples on the side, and the different required arguments below.

The invitation is accepted if it's still active and user doesn't belong to any business account yet.  For the direct call, `null` denotes a successful return value.



### Required arguments

#### For the direct call:

Argument | Description
-------- | -----------
token *String* | User's access token
id *Integer* | The invitation id (a number).
secret *String* | The secret fetched from the live notification (a string value).

#### For the Sync API command:

Argument | Description
-------- | -----------
invitation_id *Integer* | The invitation id (a number).
invitation_secret *String* | The secret fetched from the live notification (a string value).

## Reject invitation

> An example of rejecting a business invitation with a direct call:

```shell
$ curl https://todoist.com/API/v6/business/users/reject_invitation \
    -d token=0123456789abcdef0123456789abcdef01234567 \
    -d id=1234 \
    -d secret=abcdefghijklmno
```

```python
>>> import todoist
>>> api = todoist.TodoistAPI('0123456789abcdef0123456789abcdef01234567')
>>> api.business_users_reject_invitation(1234, 'abcdefghijklmno')
```

> An example of rejecting a business invitation with a Sync API command:

```shell
$ curl https://todoist.com/API/v6/sync \
    -d token=0123456789abcdef0123456789abcdef01234567 \
    -d commands='[{"type": "biz_reject_invitation", "uuid": "a1b0460a-aab3-4555-9109-779cd0cb0966", "args": {"invitation_id": 1234,  "invitation_secret": "abcdefghijklmno"}}]'
```

```python
>>> import todoist
>>> api = todoist.TodoistAPI('0123456789abcdef0123456789abcdef01234567')
>>> api.biz_invitations.reject(1234, 'abcdefghijklmno')
>>> api.commit()
```

Reject an invitation from Todoist for Business.  There are currently 2 ways to reject an invitation, either with a direct call, or by using a Sync API command, as can be seen from the examples on the side, and the different required arguments below.

The invitation is rejected and deleted.  Note that the client doesn't have to provide the user's token to reject invitation: it's enough to provide knowledge of invitation secret business account yet.  For the direct call, `null` denotes a successful return value.

### Required arguments

#### For the direct call:

Argument | Description
-------- | -----------
id *Integer* | The invitation id (a number).
secret *String* | The secret fetched from the live notification (a string value).

#### For the Sync API command:

Argument | Description
-------- | -----------
invitation_id *Integer* | The invitation id (a number).
invitation_secret *String* | The secret fetched from the live notification (a string value).


### Optional arguments

#### For the direct call:

Argument | Description
-------- | -----------
token *String* | User's access token
