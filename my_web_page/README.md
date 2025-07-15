# Run a test  website locally on Docker

Create a test website:

```
mkdir my_web_page
```

```
echo " Hello from Mick McCarthy" > my_web_page/index.html
```

Create container, mounting a volume that uses the website's directory: 
```
docker run -d --rm -p 12345:80 -v /Users/mickm/workspace/my_web_page:/usr/share/nginx/html nginx
```

Any changes made in this folder will now automatically update inside the container.
