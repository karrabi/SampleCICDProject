## پیش نیاز 
با توجه به اینکه پروژه از تکنولوژی **داکر** استفاده میکند باید حتما داکر نصب شده باشد.    

  https://docs.docker.com/engine/install/ubuntu
  ```
  # Docker Installation
  curl -fsSL https://get.docker.com/ -o get-docker.sh
  sh get-docker.sh
  ```
بهتر است از سرور ایران استفاده نشود چون داکرهاب تحریم کرده ولی در صورتی که باید حتما سرور ایران باشد با استفاده از لینک زیر یا کدهایی که بعد آن نوشتم مشکل را حل کنید: 
  ```
  https://camelcase.ir/fix-docker-download-problem-with-mirror/
  ```

  add this to /etc/docker/daemon.json
  ```
  {
    "registry-mirrors": ["https://docker.iranrepo.ir"]
  }
  ```
  then
  ```
  systemctl daemon-reload
  systemctl restart docker
  ```


  و همچنین پکیج های زیر نصب شود :    
  ```   
  sudo apt update && sudo apt install python3 git tmux python-is-python3 python3-pip
  sudo python3 -m pip install pytest==7.4.0
  ```
  طبق لینک پایین این ابزار هم باید نصب کنید:
  
  https://github.com/cli/cli/blob/trunk/docs/install_linux.md

  ```
   type -p curl >/dev/null || (sudo apt update && sudo apt install curl -y)
   curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
   && sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
   && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list    > /dev/null \
   && sudo apt update \
   && sudo apt install gh -y
  ```
  
  و بعد از آن رانر گیت هاب رو به صورت سلف هاست باید کانفیگ کنید     
  
  https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/adding-self-hosted-runners

  ```
   Repo -> Settings -> Action -> Runners -> New self hosted runner -> Linux x64 -> run all commands on server
  ```
  
  برای اجرای مداوم رانر میتوان از سشن tmux استفاده کرد.

  اگر این ارور را گرفتید:

  
   Must not run interactively with sudo
   Exiting runner

این دستور را قبلش اجرا کنید:

   ‍‍‍```export RUNNER_ALLOW_RUNASROOT="1" ```

   my os and packages:
   ```
   OS: Ubuntu 20.04.6 LTS x86_64
   gh version 2.32.1 (2023-07-24)
   git version 2.25.1
   tmux 3.0a
   Python 3.8.10
   pytest==7.4.0
   ```

## تنظیمات مربوط به repo

1. ریپو را بهتر است private کنید .
 
3. دسترسی های خواسته شده مربوط به github action از قسمت تنظیمات ریپو داده شود .


your repo -> settings -> Actions -> general

```
Required permissions
Actions permissions -> Allow all actions and reusable workflows
Workflow permissions -> Read and write permissions , Allow GitHub Actions to create and approve pull requests
disable Run workflows from fork pull requests if you see this option.
```

## کارهای مورد نیاز قبل تست

قبل از تست باید دایرکتوری github. رو با تمام محتویاتش  ( README میتواند کلا حذف شود و ضرورتی ندارد باشد )  در تمام برنچ های ریپو قرار دهید.

نکته خیلی مهم این است که این دایرکتوری با تمام محتویات داخلش (بدون اینکه فایلی کم و زیاد باشد) باید در تمام برنچ ها یکی باشد تا بعدا مشکلاتی از جمله conflict بوجود نیاید.

## شکل کلی فرایند در هنگام  پابلیش  release از برنچ main

1.  ریپو در سرور کلون شده و در دایرکتوری
``` app/ ```    نگه داری میشود

2. سپس تست های نوشته شده روی ان اجرا میگردد.
3. در صورت موفق بودن تست به وسیله پروسه اجرایی رانر گیت هاب داکر کامپوز آن اجرا میگردد .



## شکل کلی فرایند در هنگام کامیت زدن به برنچ های غیر main


1.  ریپو در سرور کلون شده و در دایرکتوری
```action-runner/_work/~```
نگه داری میشود

3. سپس تست های نوشته شده روی ان اجرا میگردد.
4.  در صورت موفق بودن تست یک  pull request از طرف این برنچ به main ارسال می شود.


## یکسری نکات اضافه

1. بهتر است هر چند روز لاگ های مربوط به github action در مسیر
‍‍‍```action-runner/_diag/~```
از سرور پاک شود .

3. سعی شود ریپو های مربوط به production کمترین راه ارتباطی را با این ریپو داشته باشند .

4. با پوش مستقیم به main تستی صورت نمیگیرد و در این صورت تنها تست در زمان پابلیش ریلیز خواهد بود . برای اینکه تست شود باید به برنچ دیگری کامیت زده شود تا کد تست شود سپس در صورت تایید با main مرج شود.

5. به صورت دیفالت action برای تمام برنچ های غیر main یکسان است به هر دلیلی اگر نمیخواید فرضا برنچ xyz در هنگام پوش کاری انجام دهد در  workflow باید این تکه کد را بدین صورت تغییر دهید.
 ```
  on:
  push:
    branches:
      - '**'
      - '!main'
      - '!xyz'
 ```
اگر نام برنچ حاوی " بود می توانید آن را بین دو  '  قرار دهید .

5. در صورتی که برنچی میخواید کدی از آن ریلیز کنید اسمش main نیست می توانید در workflow نوشته شده بجای تمام main ها اسم دیگری قرار دهید .
