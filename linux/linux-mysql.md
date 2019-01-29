mysql -u root -p

use gps6

update nav_device_info set mac_type='GT10' where mac_type='GT-10';

        proxy_redirect off;
        proxy_set_header Host                   $http_host;
        proxy_set_header X-Forwarded-Server     $host;
        proxy_set_header X-Forwarded-Proto      $scheme;
        proxy_set_header X-Real-IP              $remote_addr;
        proxy_set_header REMOTE-HOST            $remote_addr;
        proxy_set_header X-Forwarded-For        $proxy_add_x_forwarded_for;
