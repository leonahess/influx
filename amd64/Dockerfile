FROM influxdb:latest

ADD influxdb.conf .

RUN rm -f /etc/influxdb/influxdb.conf
RUN cp /influxdb.conf /etc/influxdb/


ENTRYPOINT ["/entrypoint.sh"]
CMD ["influxd"]
