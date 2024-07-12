FROM openeuler/openeuler:22.03

RUN useradd -m -s /bin/bash etcd

RUN mkdir -p /opt/bitnami/etcd/certs/ && chown -R etcd:etcd /opt/bitnami/etcd/certs/
RUN mkdir -p /opt/bitnami/etcd/data/ && chown -R etcd:etcd /opt/bitnami/etcd/data/

USER etcd

ADD etcd /usr/local/bin/
ADD etcdctl /usr/local/bin/
ADD etcdutl /usr/local/bin/

WORKDIR /var/etcd/
WORKDIR /var/lib/etcd/

EXPOSE 2379 2380

# Define default command.
CMD ["/usr/local/bin/etcd"]