ARG VARIANT="18.4.0-xe"
FROM oracle/database:${VARIANT}

COPY ./ol7_developer_EPEL.repo /etc/yum.repos.d/
RUN yum reinstall -y \
        glibc-common \
    && yum install -y \
        openssh-server \
        supervisor \
        nano
        # cockpit

# ロケール及びタイムゾーンを日本に設定
RUN localedef -f UTF-8 -i ja_JP ja_JP.UTF-8 \
    && echo 'LANG=ja_JP.UTF-8' > /etc/locale.conf \
    && ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
ENV LANG="ja_JP.UTF-8" \
    LANGUAGE="ja_JP:ja" \
    LC_ALL="ja_JP.UTF-8" \
    TZ="Asia/Tokyo"

# Entrypoint
COPY entrypoint.d /entrypoint.d
COPY entrypoint.sh /
RUN chmod +x /entrypoint.sh \
    && chmod +x -R /entrypoint.d

# # Supervisor
# COPY supervisord.conf /etc/supervisor/
# RUN mkdir -p /var/run/supervisor /var/log/supervisor \
#     && chmod -R 755 /var/run/supervisor /var/log/supervisor

# SSH
RUN mkdir -p /etc/ssh/ssh_host_keys \
    && sed -i 's|HostKey /etc/ssh/|HostKey /etc/ssh/ssh_host_keys/|' /etc/ssh/sshd_config

RUN echo "root:root" | chpasswd \
    && echo "oracle:oracle" | chpasswd

# CMD exec $ORACLE_BASE/$RUN_FILE

# /opt/oracle/product/18c/dbhomeXE/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
#                                      /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin

COPY ./oracle_env.sh /etc/profile.d/
# RUN echo "export ORACLE_BASE=/opt/oracle" > /etc/profile.d/oracle_env.sh \
#     && echo "export ORACLE_HOME=/opt/oracle/product/18c/dbhomeXE" >> /etc/profile.d/oracle_env.sh \
#     && echo "export ORACLE_SID=XE" >> /etc/profile.d/oracle_env.sh \
#     && echo "export PATH=\$PATH:/opt/oracle/product/18c/dbhomeXE/bin" >> /etc/profile.d/oracle_env.sh

#     NLS_LANG=AMERICAN_AMERICA.AL32UTF8

EXPOSE 22
ENTRYPOINT ["/entrypoint.sh"]
# CMD ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/supervisord.conf"]
# CMD ["bash"]
CMD exec $ORACLE_BASE/$RUN_FILE