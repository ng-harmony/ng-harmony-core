# Ng-Harmony
============

## Development

These is the bazz-klasses for writing nice ES-Next Angular ~2 ... you'll import and utilize Angular 1 and all the Angular 1 ecosystem or whatever libraries you might have built, but you can write your ongoing/new apps in this Angular 2 (-like) style and migrate easily should you choose to.

![Harmony = 6 + 7;](logo.png "Harmony - Fire in my eyes")

## Concept

A base-class collection for OO programming in ES6 with angular.
Use it in conjunction with

	* [literate-programming](http://npmjs.org/packages/literate-programming "click for npm-package-homepage") to write markdown-flavored literate JS, HTML and CSS
	* [jspm](https://www.npmjs.com/package/jspm "click for npm-package-homepage") for a nice solution to handle npm-modules with ES6-Module-Format-Loading ...

## Files

This serves as literate-programming compiler-directive

[build/index.js](#Compilation "save:")

## Compilation

```javascript
import { Mixin } from "ng-harmony-decorator";
import * as _ from "ng-harmony-util";
```

_Harmony_ is the ng-base-class for all other endeavours.

```javascript
@Mixin(_.TimeUtil, _.NumberUtil, _.AsyncUtil, _.TypeCheckUtil)
export class Harmony {
	constructor (...args) {
        this.constructor.$inject.forEach((injectee, i) => {
			this[injectee] = args[i];
		});
		this._constructed = this._timestamp;
	}
	get name () {
		return `${this.constructor.name}::${this._constructed}::${this._random}`;
	}
	get _name () {
		let _name = this.name.split("::");
		return {
			fn: _name[0],
			ts: _name[1],
			id: _name[2]
		}
	}
```

Getter and Setter for the static $inject variable

```javascript
	static get $inject () {
		return this._$inject || [];
	}
	static set $inject (injectees) {
		let _injectees = [];
		if (!Array.isArray(injectees)) {
			injectees = [injectees];
		}
		for (let [i, injecteeStr] of injectees.entries()) {
			let truthy = true;
			for (let [j, _injecteeStr] of this.$inject.entries()) {
				if (injecteeStr === _injecteeStr) {
					truthy = false;
				}
			}
			if (truthy) {
				if(injecteeStr.charAt(0) !== "-") {
					_injectees.push(injecteeStr);
				} else {
					let j = this.$inject.indexOf(injecteeStr.slice(1));
					if (!!~j) {
						this._$inject.splice(j, 1);
					}
				}
			}
		}
		this._$inject = this.$inject.concat(_injectees);
	}
```
Setter for the ng-registration of a service or controller
```javascript
	static set $register (descriptor) {
		Object.keys(descriptor).forEach((module, i) => {
			angular.module(module)[descriptor[module].type](descriptor[module].name, this);
		});
	}
```
A nice toString foo that should in theory pretty nicely return the Classe's name as declared
```javascript
	toString () {
		return this.name || super.toString().match(/function\s*(.*?)\(/)[1];
	}
}
```

The Controller Base-Class is a starting point for all ng-controllers.

```javascript
export class Controller extends Harmony {
    static set $register(descriptor) {
        Object.getOwnPropertyNames(descriptor).forEach((module) => {
            angular.module(module).controller(descriptor[module].name, this);
        });
    }
	constructor (...args) {
		super(...args);
		
		let proto = this.constructor.prototype;
		if (this.constructor.CTRL_AS) this.$scope[this.constructor.CTRL_AS] = {};
		
		Object.getOwnPropertyNames(proto).forEach((key, i) => {
			if (typeof proto[key] === "function" &&
				key[0] === "$") {
				if (this.constructor.CTRL_AS) {
					this.$scope[this.constructor.CTRL_AS][key.slice(1)] = (..._args) => {
						return proto[key].apply(this, _args);
					};
				} else {
					this.$scope[key] = (..._args) => {
						return proto[key].apply(this, _args);
					}	
				}
			}
		});
	}
	_digest () {
		try { this.$scope.$digest(); }
		catch (ngEx) {}
	}
}
Controller.$inject = "$scope";
```

The _Service_ Class is a tiny base for Services that don't extend the more sophisticated DataServices

```javascript
export class Service extends Harmony {
	constructor (...args) {
		super(...args);
	}
	static set $register(descriptor) {
		Object.keys(descriptor).forEach((module) => {
			angular.module(module)[descriptor[module].type || "service"](descriptor[module].name, this);
		});
	}
}
```

## CHANGELOG
*0.2.0* New README --- considered usable/semi-stable
*0.1.3* Debug Publish Bump
*0.1.2* Reintroducing Ctrl plus Service in core ... just out of reason
*0.1.0* Base Class, now with default logger

## MIGRATION CHANGELOG ng-harmony

*0.2.1* Add conditional initialize call to default base-constructor for better mixin-support

*0.3.2* About to pick up development again, new logo

*<0.4* Debuggin for [demo todo-mvc page on github.io](http://ng-harmony.github.io/ng-harmony)

*0.4.4* Enhancing mixing in with constructor-mixin-support ... mixin-constructors get called in Harmony-super-constructor

*0.4.5* Practical difficulties with mixing constructors, getting rid of it again

*0.4.6* Correcting Mixin plus adding Implement (Interface support), tweaking .babelrc
