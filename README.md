# django-tutoriral

## nginx

```
upstream app_server_djangoapp {
    server localhost:8002 fail_timeout=0; 
}

server {
    listen 80;
    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;

        if (!-f $request_filename) {
            proxy_pass http://app_server_djangoapp;
            break;
        }
    }
}
```

## gunicorn

```
CONFIG = {
    'mode': 'wsgi',
    'working_dir': '/var/www/mysite',
    'python': '/usr/bin/python',
    'args': (
        '--bind=127.0.0.1:8002',
        '--workers=16',
        '--timeout=60',
        'mysite.wsgi:application',
    ),
}
```

### django