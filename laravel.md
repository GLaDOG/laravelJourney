# Whoops! There was an error.

```
UnexpectedValueException
The stream or file "/path/to/your/project/storage/logs/laravel.log" could not be opened: failed to open stream: Permission denied
```

## 原因

在Laravel 项目中, 当前用户和 `www-data` (the webserver) 用户组对于 `storage` 目录下的所有目录和文件都要有 `writable` 权限. 但是使用 `composer create-project laravel/laravel yourApp --prefer-dist "5.5.*"` 安装 `Laravel` 项目后, `www-data` (the webserver) 用户组没有针对 `storage` 目录下的所有目录和文件的 `writable` 权限, 所以导致了上述错误. ![Laravel-error.png](/img/Laravel-error.png)

如果使用浏览器访问应用时, 出现类似的错误. 代表着 `www-data` (the webserver) 用户组不可写 `storage` 目录. 如果在命令行使用 `php artisan` 时, 出现类似的错误. 代表着当前用户不可写 `storage` 目录. 因此 `writable` 权限需要赋予当前用户和 `www-data` (The webserver)用户组.

## 解决办法

可以参照如下步骤进行操作:
1. 切换到项目目录下:
 `cd /path/to/your/app/`
2. 更改 `storage` 目录的拥有者和用户组:
 `sudo chown -R yourusername:www-data storage`
 `-R` 代表级联操作, 命令对 `storage` 目录下的子目录和文件生效.
 `yourusername` 是当前用户的用户名.
3. 添加当前用户和 `www-data` 对 `storage` 的 `writable` 权限.
 `sudo chmod -R ug+w storage`
4. 另外, 建议清理应用缓存, 并重新生成 Laravel autoload 文件:
 `php artisan cache:clear`
 `php artisan dump-autoload`
5. 最后, 重新生成 composer autoload 文件:
 `composer dump-autoload`

翻译自[“laravel.log” could not be opened: failed to open stream](https://stackoverflow.com/questions/24055056/laravel-log-could-not-be-opened-failed-to-open-stream)
