.DEFAULT_GOAL := all

.PHONY: p
p: package
package:
	helm package trident-installer

.PHONY: i
i: index
index:
	helm repo index .

.PHONY: h
h: help
help:
	@echo "Update app and chart versions accordingly.  Then run make package index.  Test, commit and push to remote repo."

.PHONY: all
all: package index
