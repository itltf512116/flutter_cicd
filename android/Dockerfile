# 这个镜像包含了sdk ndk
FROM cimg/android:2022.12-ndk

USER root

# 当前因为项目用到了30.0.3，所以这里需要安装下，避免打包app时安装，如果用不到，可以去掉安装
RUN 	echo y | ${CMDLINE_TOOLS_ROOT}/sdkmanager "build-tools;30.0.3"

ARG flutter_version

ENV FLUTTER_HOME=/usr/local/bin/flutter \
    FLUTTER_VERSION=$flutter_version
ENV FLUTTER_ROOT=$FLUTTER_HOME

ENV PATH ${PATH}:${FLUTTER_HOME}/bin:${FLUTTER_HOME}/bin/cache/dart-sdk/bin
# RUN echo ${PATH}

# 直接下载stable包比如3.7.0
RUN wget https://storage.flutter-io.cn/flutter_infra_release/releases/stable/linux/flutter_linux_${FLUTTER_VERSION}-stable.tar.xz && \
    tar xf flutter_linux_${FLUTTER_VERSION}-stable.tar.xz -C /usr/local/bin && \
    rm flutter_linux_${FLUTTER_VERSION}-stable.tar.xz \
    && git config --global --add safe.directory ${FLUTTER_HOME}


ENV PUB_HOSTED_URL=https://pub.flutter-io.cn
ENV FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
#RUN  git clone --depth 1 --branch ${FLUTTER_VERSION} https://github.com/flutter/flutter.git ${FLUTTER_HOME}

# RUN yes | flutter doctor --android-licenses \
#     && flutter doctor \
#     && chown -R root:root ${FLUTTER_HOME}

# 预处理android的缓存
RUN flutter precache --no-web --no-linux --no-windows --no-fuchsia --no-ios --no-macos

# Install fastlane.
RUN apt update \
    && apt install -y ruby ruby-dev rubygems \
    && apt clean -y \
    && gem install fastlane -NV \
    && gem install bundler \
    && mkdir -p /cache/gradle && chmod 777 /cache/gradle

# 设置gradle 缓存目录，ci中可以挂载出去
ENV GRADLE_USER_HOME=/cache/gradle