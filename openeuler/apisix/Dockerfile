#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

FROM openeuler/openeuler:22.03

ARG APISIX_VERSION=3.9.1
LABEL apisix_version="${APISIX_VERSION}"
COPY ./apisix-3.9.1-0.el7.x86_64.rpm /tmp
COPY ./openresty.repo /etc/yum.repos.d/openresty.repo

RUN dnf update -y \
	&& dnf install -y wget\
	&& dnf install -y /tmp/apisix-3.9.1-0.el7.x86_64.rpm\
	&& dnf clean all -y\
	&& sed -i 's/PASS_MAX_DAYS\t99999/PASS_MAX_DAYS\t60/g' /etc/login.defs

COPY ./install-brotli.sh /install-brotli.sh
RUN chmod +x /install-brotli.sh \
    && cd / && ./install-brotli.sh && rm -rf /install-brotli.sh

WORKDIR /usr/local/apisix

ENV PATH=$PATH:/usr/local/openresty/luajit/bin:/usr/local/openresty/nginx/sbin:/usr/local/openresty/bin

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /usr/local/apisix/logs/access.log \
    && ln -sf /dev/stderr /usr/local/apisix/logs/error.log \
    && rm /usr/local/openresty/bin/etcdctl

EXPOSE 9080 9443

COPY ./docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh

ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["docker-start"]

STOPSIGNAL SIGQUIT
