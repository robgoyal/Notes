# Virtual Hosts
Virtual hosts allow you to host multiple applications at a single IP address. 

To differentiate between applications, the Host request header includes the domain that you're trying to access. When the application sees that you're accessing `foo.example.com`, it'll redirect you to a different application compared to `bar.example.com`.

