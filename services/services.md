## List of active services and running
```sh
systemctl | grep "active running"
```
## Service Status Start Stop enable disable
```sh
sudo systemctl Status php8.1-fpm.service
sudo systemctl Start php8.1-fpm.service
sudo systemctl Stop php8.1-fpm.service
sudo systemctl enable php8.1-fpm.service
sudo systemctl disable php8.1-fpm.service
```
