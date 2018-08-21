---
layout: post
title:  "RabbitMQ"
categories: RabbitMQ

categories:
  - RabbitMQ

tags:
  - Queues
  - RabbitMQ
  - Rabbit
  - metadata


excerpt: Weird rabbity stuff
---

**Can't login to RMQ**
`rabbitmqctl list_users` - ensure that the user exists
`rabbitmqctl change_password username password` - to the correct change_password
Still can't login?
Ensure that there's only one tag on the user using the "list_users"
eg: if you have
```
rabbitmqctl list_users
Listing users
vishal	[administrator,monitoring]
```

Logins will not work and they will fail, there must be **one** and only __one__ tag on a single user.
The official documentation is somewhat un-helpful here.

>Note that since "administrator" does everything "monitoring" does, and "monitoring" does everything "management" does, you frequently only need to give each user a maximum of one tag.

See also
[Official Rabbit's documentation](https://www.rabbitmq.com/management.html)

---

| Quirky thing        | Explanation  |
| ------------- |:-------------:|
| Can't login to rabbitmq      | Do something  |
