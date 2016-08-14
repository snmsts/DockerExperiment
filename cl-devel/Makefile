tagname=some-common-lisp
name=cl-devel
help:
	@echo "[WATCH OUT] this script will copy ~/.emacs.d and ~/.ssh/ to container ."
	@echo ""
	@echo "my .emac.d/init.el contains"
	@echo ""
	@echo '  (setq inferior-lisp-program "ros -Q run")'
	@echo '  (load "~/.roswell/helper.el")'
	@echo "  (slime-setup '(slime-fancy slime-indentation))"
	@echo ""
	@echo "if prepared type"
	@echo ""
	@echo "  make all"
all:
	rm -rf .ssh
	cp -r ~/.ssh .
	rm -rf .emacs.d
	cp -r ~/.emacs.d .
	docker build -f Dockerfile -t $(tagname) .
	rm -rf .ssh
	rm -rf .emacs.d
no-cache:
	rm -rf .ssh
	cp -r ~/.ssh .
	rm -rf .emacs.d
	cp -r ~/.emacs.d .
	docker build --no-cache -f Dockerfile -t $(tagname) .
	rm -rf .ssh
	rm -rf .emacs.d
run:
	mkdir work||true
	docker run -v /var/run/docker.sock:/var/run/docker.sock -v $(PWD)/work:/home/dev/work -d -P --name $(name) $(tagname)
ssh:
	ssh -l dev -i ~/.ssh/id_rsa -o "UserKnownHostsFile /dev/null" -o StrictHostKeyChecking=no 0.0.0.0 -p `docker port $(name)|awk 'NR==1'|awk -F: '{print $$2}'` 
kill:
	docker kill `docker ps -q -f name=$(name)`||true
	docker rm $(name)||true
restart:
	make kill||true
	make run

.PHONY: help all no-cache run ssh kill restart
