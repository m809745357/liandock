FROM ubuntu:20.04

LABEL maintainer="809745357@qq.com"

ARG WWWGROUP
ARG WWWUSER

WORKDIR /var/www/html

# 更新镜像
RUN sed -i -E 's/http:\/\/(archive|security).ubuntu.com/http:\/\/mirrors.aliyun.com/g' /etc/apt/sources.list

# 更新
RUN apt-get update

# 安装ZSH
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get install -y gnupg gosu curl ca-certificates zip unzip git zsh supervisor sqlite3 libcap2-bin libpng-dev python2 tzdata

# 调节时区
RUN ln -fs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 公共秘钥
RUN mkdir -p ~/.gnupg \
    && chmod 600 ~/.gnupg \
    && echo "disable-ipv6" >> ~/.gnupg/dirmngr.conf \
    && echo "keyserver hkp://keyserver.ubuntu.com:80" >> ~/.gnupg/dirmngr.conf \
    && gpg --recv-key 0x14aa40ec0831756756d7f66c4f4ea0aae5267a6c \
    && gpg --export 0x14aa40ec0831756756d7f66c4f4ea0aae5267a6c > /usr/share/keyrings/ppa_ondrej_php.gpg

# 增加PHP source
RUN echo "deb [signed-by=/usr/share/keyrings/ppa_ondrej_php.gpg] https://launchpad.proxy.ustclug.org/ondrej/php/ubuntu focal main" > /etc/apt/sources.list.d/ppa_ondrej_php.list

# 更新
RUN apt-get update

# 安装PHP
RUN apt-get install -y php7.2-cli php7.2-dev \
       php7.2-pgsql php7.2-sqlite3 php7.2-gd \
       php7.2-curl php7.2-memcached \
       php7.2-imap php7.2-mysql php7.2-mbstring \
       php7.2-xml php7.2-zip php7.2-bcmath php7.2-soap \
       php7.2-intl php7.2-readline php7.2-pcov \
       php7.2-msgpack php7.2-igbinary php7.2-ldap \
       php7.2-redis php7.2-xdebug

# 提权
RUN setcap "cap_net_bind_service=+ep" /usr/bin/php7.2

# 安装composer
RUN php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer

# 安装ohmyzsh
RUN git clone https://gitclone.com/github.com/ohmyzsh/ohmyzsh.git ~/.oh-my-zsh \  
    && cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc \
    && sed -i "s/robbyrussell/ys/" ~/.zshrc \
    && git clone https://gitclone.com/github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions \
    && git clone https://gitclone.com/github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting \
    && sed -i "s/plugins=(git.*)$/plugins=(git zsh-syntax-highlighting zsh-autosuggestions)/" ~/.zshrc

# 安装vim
RUN apt-get install -y vim \
    && echo "set nu" >> ~/.vimrc

# 清除apt缓存
RUN apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN groupadd --force -g $WWWGROUP sail
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u 1337 sail
RUN usermod -u $WWWUSER -s /bin/zsh sail

ENTRYPOINT ["/bin/zsh"]
