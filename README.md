# React Resolver 

[![Build Status](https://travis-ci.org/gilesbradshaw/react-resolver.svg?branch=travistest)](https://travis-ci.org/gilesbradshaw/react-resolver)

> Isomorphic library to lazy-load data for React components

Forked and nodified to use CSP channels rather than promises

````
describe("Resolver", function() {
  describe(".nested", function() {
    it("should render nested elements", function(done) {
        @dataDependencies({
          user: ()=>
            go(function* (){
              return "Ernie";
            })
        })
        @displayName("TestElement1")
        class Element1 extends Component {
            render(){
                return <span>{this.props.user}</span>;
            }
        };

        @dataDependencies({
          user: ()=>
            go(function* (){
              return "Eric";
            })
        })
        @displayName("TestElement")
        class Element extends Component {
            render(){
                return <span>{this.props.user}<Element1/></span>;
            }
        };
        go(function*(){
            const string = yield Resolver.renderToStaticMarkup(<Element />);
            assert.equal("<span>Eric<span>Ernie</span></span>",string.toString());
            done();
        });
    });
  });
});

describe("Resolver", function() {
  describe(".dom", function() {
    it("should render a channel into dom", function(done) {
        const ch = chan();
        @dataDependencies({
            user: ()=>ch
        }) 
        @displayName("TestElement")
        class Element extends Component {
            render(){
                return <span>{this.props.user}</span>;
            }
        };
        const resolver = new Resolver(); 
        Resolver.render(
            <Element/>
            , document.body, resolver);

         go(function* (){
            yield put(ch,"Ernie");
            yield timeout(0);
            assert.equal(document.querySelector('span').innerHTML, 'Ernie');
            yield put(ch,"Wise");
            yield timeout(0);
            assert.equal(document.querySelector('span').innerHTML, 'Wise');
            done();
         });     
    });
  });
});

````
## Current problem
js-csp seems to operate slightly differently when built than when run with babel/require

## Development

If you'd like to contribute to this project, all you need to do is clone
this project and run:

```shell
$ npm install
$ npm test
```
