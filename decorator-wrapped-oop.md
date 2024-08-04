Decorator là một cách để thêm hoặc mở rộng chức năng của các đối tượng một cách linh hoạt mà không thay đổi mã nguồn của lớp ban đầu. Nó cho phép bạn "bọc" một đối tượng hiện có với một lớp mới để cung cấp các chức năng bổ sung.
### Ví dụ:
Có một chức năng gửi tin, ban đầu chỉ có email, sau đó thêm sms và slack.
```python
from abc import ABC, abstractmethod

class NotifierInterface(ABC):
    @abstractmethod
    def send(self, message):
        pass

class Notifier(NotifierInterface):
    def __init__(self, inputs):
        self.inputs = inputs
    def send(self, message):
        for input in self.inputs:
            print(f"send {message} to {input}")

class EmailSend(Notifier):
    def __init__(self, emails):
        super().__init__(emails)
        self.emails = emails

class SmsSend(Notifier):
    def __init__(self, sms):
        super().__init__(sms)
        self.sms = sms

if __name__ == "__main__":
    emails = ["tienbm.ho@mcredit.com.vn"]
    sms = ["0966058476"]
    noti1 = EmailSend(emails)
    noti2 = SmsSend(sms)
    noti1.send("sfdfdf")
    noti2.send("sfdfdf")
```
### Khó khăn:
Người dùng lại muốn gửi theo cặp: `sms & slack` hoặc `sms & email` hoặc `email & slack`
Chương trình đã viết không thể đáp ứng được nhu cầu mở rộng, code không thể sử dụng lại được có thể phải phát triển mới.
### Solution:
Sử dụng `Combined`: ý tưởng sẽ viết một hàm sao cho có thể ghép đôi như mong muốn và sẽ gửi một cách đồng thời.
```python
from abc import ABC, abstractmethod

class NotifierInterface(ABC):
    @abstractmethod
    def send(self, message):
        pass
class EmailNotifier(NotifierInterface):
    def __init__(self, emails):
        self.emails = emails

    def send(self, message):
        for email in self.emails:
            print(f"Sending '{message}' to {email}")
class SMSNotifier(NotifierInterface):
    def __init__(self, phone_numbers):
        self.phone_numbers = phone_numbers

    def send(self, message):
        for phone in self.phone_numbers:
            print(f"Sending SMS '{message}' to {phone}")
class SlackNotifier(NotifierInterface):
    def __init__(self, slack_users):
        self.slack_users = slack_users

    def send(self, message):
        for user in self.slack_users:
            print(f"Sending Slack message '{message}' to {user}")
class CombinedNotifier(NotifierInterface):
    def __init__(self):
        self.notifiers = []

    def add_notifier(self, notifier):
        self.notifiers.append(notifier)

    def send(self, message):
        for notifier in self.notifiers:
            notifier.send(message)

if __name__ == "__main__":
    emails = ["tienbm.ho@mcredit.com.vn"]
    phone_numbers = ["0966058476"]
    slack_users = ["user1_slack", "user2_slack"]

    email_notifier = EmailNotifier(emails)
    sms_notifier = SMSNotifier(phone_numbers)
    slack_notifier = SlackNotifier(slack_users)

    combined_notifier = CombinedNotifier()
    combined_notifier.add_notifier(email_notifier)
    combined_notifier.add_notifier(sms_notifier)
    combined_notifier.add_notifier(slack_notifier)
    combined_notifier.send("This is an important notification!")
```
### Khó khăn:
Người dùng lại muốn gửi tuần tự không đồng thời và tương lai muốn có ghi log sau mỗi bước ....
### Solution
Sử dụng `Decorator`.\
Có một lưu ý: cần một class khởi tạo vì các class: `SmsDecorator` hay `EmailDecorator` phải truyền một `wrapped` .\
Khi gọi tới `SmsDecorator` phải truyền `wrapped` và `phones`.\
Nếu `wrapped` là một class `EmailDecorator` => nó sẽ lại cần `wrapped` và `emails` .... do đó quá trình này cứ liên tục nếu chúng ta không có một class rỗng để kết thúc. `InitWrapped` là giải pháp.
```python
class SmsDecorator(NotifierDecorator):
    def __init__(self, wrapped, phones):
        super().__init__(wrapped)
        self.phones = phones
    def send(self, message):
        super().send(message)
        for phone in self.phones:
            print(f"send {message} to {phone}")
```
```python
from abc import ABC, abstractmethod

class NotifierInterface(ABC):
    @abstractmethod
    def send(self, message):
        pass
class InitWrapped(NotifierInterface):
    def send(self, message):
        pass
class NotifierDecorator(NotifierInterface):
    def __init__(self, wrapped):
        self.decorated_notifier = wrapped
    def send(self, message):
        self.decorated_notifier.send(message)

class SmsDecorator(NotifierDecorator):
    def __init__(self, wrapped, phones):
        super().__init__(wrapped)
        self.phones = phones
    def send(self, message):
        super().send(message)
        for phone in self.phones:
            print(f"send {message} to {phone}")

class EmailDecorator(NotifierDecorator):
    def __init__(self, wrapped, emails):
        super().__init__(wrapped)
        self.emails = emails
    def send(self, message):
        # super().send(message)
        for email in self.emails:
            print(f"send {message} to {email}")

class SlackDecorator(NotifierDecorator):
    def __init__(self, wrapped, slack_users):
        super().__init__(wrapped)
        self.slack_users = slack_users
    def send(self, message):
        for slack_user in slack_users:
            print(f"send {message} to {slack_user}")

if __name__ == "__main__":
    emails = ["tienbm.ho@xxx.com"]
    phones = ['0966792384535']
    slack_users = ['sl1']
    init_wrapped = InitWrapped()
    sms_email = SmsDecorator(EmailDecorator(init_wrapped, emails), phones)
    sms_email.send("Notification")
```
