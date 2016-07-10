all:
	rm -rf .ssh
	cp -r ~/.ssh .
	docker build -f Dockerfile -t some-common-lisp .
	rm -rf .ssh
no-cache:
	rm -rf .ssh
	cp -r ~/.ssh .
	docker build --no-cache -f Dockerfile -t some-common-lisp .
	rm -rf .ssh
run:
	docker run -d -P --name cl-devel some-common-lisp
ssh:
	ssh -o StrictHostKeyChecking=no root@0.0.0.0 -p `docker port cl-devel|awk 'NR==1'|awk -F: '{print $$2}'`
kill:
	docker kill `docker ps -q -f name=cl-devel`||true
	docker rm cl-devel||true
restart:
	make kill||true
	make run

.PHONY: all no-cache run ssh kill restart
