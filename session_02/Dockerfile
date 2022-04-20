FROM node:16-alpine3.11

RUN apk add curl
# RUN curl

RUN pwd
WORKDIR /home/node
RUN pwd

USER node
RUN cd /home/node && touch test2
RUN cd /home/node && ls -al

USER root
RUN touch test
RUN ls -al

EXPOSE 80
EXPOSE 443

ENV VARIABLE_NAME="Collaboratori"

CMD date

COPY --chown=root:node ./test.txt /test.txt
RUN cat /test.txt
