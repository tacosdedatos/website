---
layout: post
current: post
cover:  assets/blogposts/blog-011.png
navigation: True
title: Dueño de mis datos - Capítulo Spotify 
date: 2020-01-30 10:00:00
tags: [blog, Spotify, ggplot2, r,]
class: post-template
subclass: 'post tag-blog'
author: nerudista
---
<script>(function() {
  // If window.HTMLWidgets is already defined, then use it; otherwise create a
  // new object. This allows preceding code to set options that affect the
  // initialization process (though none currently exist).
  window.HTMLWidgets = window.HTMLWidgets || {};

  // See if we're running in a viewer pane. If not, we're in a web browser.
  var viewerMode = window.HTMLWidgets.viewerMode =
      /\bviewer_pane=1\b/.test(window.location);

  // See if we're running in Shiny mode. If not, it's a static document.
  // Note that static widgets can appear in both Shiny and static modes, but
  // obviously, Shiny widgets can only appear in Shiny apps/documents.
  var shinyMode = window.HTMLWidgets.shinyMode =
      typeof(window.Shiny) !== "undefined" && !!window.Shiny.outputBindings;

  // We can't count on jQuery being available, so we implement our own
  // version if necessary.
  function querySelectorAll(scope, selector) {
    if (typeof(jQuery) !== "undefined" && scope instanceof jQuery) {
      return scope.find(selector);
    }
    if (scope.querySelectorAll) {
      return scope.querySelectorAll(selector);
    }
  }

  function asArray(value) {
    if (value === null)
      return [];
    if ($.isArray(value))
      return value;
    return [value];
  }

  // Implement jQuery's extend
  function extend(target /*, ... */) {
    if (arguments.length == 1) {
      return target;
    }
    for (var i = 1; i < arguments.length; i++) {
      var source = arguments[i];
      for (var prop in source) {
        if (source.hasOwnProperty(prop)) {
          target[prop] = source[prop];
        }
      }
    }
    return target;
  }

  // IE8 doesn't support Array.forEach.
  function forEach(values, callback, thisArg) {
    if (values.forEach) {
      values.forEach(callback, thisArg);
    } else {
      for (var i = 0; i < values.length; i++) {
        callback.call(thisArg, values[i], i, values);
      }
    }
  }

  // Replaces the specified method with the return value of funcSource.
  //
  // Note that funcSource should not BE the new method, it should be a function
  // that RETURNS the new method. funcSource receives a single argument that is
  // the overridden method, it can be called from the new method. The overridden
  // method can be called like a regular function, it has the target permanently
  // bound to it so "this" will work correctly.
  function overrideMethod(target, methodName, funcSource) {
    var superFunc = target[methodName] || function() {};
    var superFuncBound = function() {
      return superFunc.apply(target, arguments);
    };
    target[methodName] = funcSource(superFuncBound);
  }

  // Add a method to delegator that, when invoked, calls
  // delegatee.methodName. If there is no such method on
  // the delegatee, but there was one on delegator before
  // delegateMethod was called, then the original version
  // is invoked instead.
  // For example:
  //
  // var a = {
  //   method1: function() { console.log('a1'); }
  //   method2: function() { console.log('a2'); }
  // };
  // var b = {
  //   method1: function() { console.log('b1'); }
  // };
  // delegateMethod(a, b, "method1");
  // delegateMethod(a, b, "method2");
  // a.method1();
  // a.method2();
  //
  // The output would be "b1", "a2".
  function delegateMethod(delegator, delegatee, methodName) {
    var inherited = delegator[methodName];
    delegator[methodName] = function() {
      var target = delegatee;
      var method = delegatee[methodName];

      // The method doesn't exist on the delegatee. Instead,
      // call the method on the delegator, if it exists.
      if (!method) {
        target = delegator;
        method = inherited;
      }

      if (method) {
        return method.apply(target, arguments);
      }
    };
  }

  // Implement a vague facsimilie of jQuery's data method
  function elementData(el, name, value) {
    if (arguments.length == 2) {
      return el["htmlwidget_data_" + name];
    } else if (arguments.length == 3) {
      el["htmlwidget_data_" + name] = value;
      return el;
    } else {
      throw new Error("Wrong number of arguments for elementData: " +
        arguments.length);
    }
  }

  // http://stackoverflow.com/questions/3446170/escape-string-for-use-in-javascript-regex
  function escapeRegExp(str) {
    return str.replace(/[\-\[\]\/\{\}\(\)\*\+\?\.\\\^\$\|]/g, "\\$&");
  }

  function hasClass(el, className) {
    var re = new RegExp("\\b" + escapeRegExp(className) + "\\b");
    return re.test(el.className);
  }

  // elements - array (or array-like object) of HTML elements
  // className - class name to test for
  // include - if true, only return elements with given className;
  //   if false, only return elements *without* given className
  function filterByClass(elements, className, include) {
    var results = [];
    for (var i = 0; i < elements.length; i++) {
      if (hasClass(elements[i], className) == include)
        results.push(elements[i]);
    }
    return results;
  }

  function on(obj, eventName, func) {
    if (obj.addEventListener) {
      obj.addEventListener(eventName, func, false);
    } else if (obj.attachEvent) {
      obj.attachEvent(eventName, func);
    }
  }

  function off(obj, eventName, func) {
    if (obj.removeEventListener)
      obj.removeEventListener(eventName, func, false);
    else if (obj.detachEvent) {
      obj.detachEvent(eventName, func);
    }
  }

  // Translate array of values to top/right/bottom/left, as usual with
  // the "padding" CSS property
  // https://developer.mozilla.org/en-US/docs/Web/CSS/padding
  function unpackPadding(value) {
    if (typeof(value) === "number")
      value = [value];
    if (value.length === 1) {
      return {top: value[0], right: value[0], bottom: value[0], left: value[0]};
    }
    if (value.length === 2) {
      return {top: value[0], right: value[1], bottom: value[0], left: value[1]};
    }
    if (value.length === 3) {
      return {top: value[0], right: value[1], bottom: value[2], left: value[1]};
    }
    if (value.length === 4) {
      return {top: value[0], right: value[1], bottom: value[2], left: value[3]};
    }
  }

  // Convert an unpacked padding object to a CSS value
  function paddingToCss(paddingObj) {
    return paddingObj.top + "px " + paddingObj.right + "px " + paddingObj.bottom + "px " + paddingObj.left + "px";
  }

  // Makes a number suitable for CSS
  function px(x) {
    if (typeof(x) === "number")
      return x + "px";
    else
      return x;
  }

  // Retrieves runtime widget sizing information for an element.
  // The return value is either null, or an object with fill, padding,
  // defaultWidth, defaultHeight fields.
  function sizingPolicy(el) {
    var sizingEl = document.querySelector("script[data-for='" + el.id + "'][type='application/htmlwidget-sizing']");
    if (!sizingEl)
      return null;
    var sp = JSON.parse(sizingEl.textContent || sizingEl.text || "{}");
    if (viewerMode) {
      return sp.viewer;
    } else {
      return sp.browser;
    }
  }

  // @param tasks Array of strings (or falsy value, in which case no-op).
  //   Each element must be a valid JavaScript expression that yields a
  //   function. Or, can be an array of objects with "code" and "data"
  //   properties; in this case, the "code" property should be a string
  //   of JS that's an expr that yields a function, and "data" should be
  //   an object that will be added as an additional argument when that
  //   function is called.
  // @param target The object that will be "this" for each function
  //   execution.
  // @param args Array of arguments to be passed to the functions. (The
  //   same arguments will be passed to all functions.)
  function evalAndRun(tasks, target, args) {
    if (tasks) {
      forEach(tasks, function(task) {
        var theseArgs = args;
        if (typeof(task) === "object") {
          theseArgs = theseArgs.concat([task.data]);
          task = task.code;
        }
        var taskFunc = tryEval(task);
        if (typeof(taskFunc) !== "function") {
          throw new Error("Task must be a function! Source:\n" + task);
        }
        taskFunc.apply(target, theseArgs);
      });
    }
  }

  // Attempt eval() both with and without enclosing in parentheses.
  // Note that enclosing coerces a function declaration into
  // an expression that eval() can parse
  // (otherwise, a SyntaxError is thrown)
  function tryEval(code) {
    var result = null;
    try {
      result = eval(code);
    } catch(error) {
      if (!error instanceof SyntaxError) {
        throw error;
      }
      try {
        result = eval("(" + code + ")");
      } catch(e) {
        if (e instanceof SyntaxError) {
          throw error;
        } else {
          throw e;
        }
      }
    }
    return result;
  }

  function initSizing(el) {
    var sizing = sizingPolicy(el);
    if (!sizing)
      return;

    var cel = document.getElementById("htmlwidget_container");
    if (!cel)
      return;

    if (typeof(sizing.padding) !== "undefined") {
      document.body.style.margin = "0";
      document.body.style.padding = paddingToCss(unpackPadding(sizing.padding));
    }

    if (sizing.fill) {
      document.body.style.overflow = "hidden";
      document.body.style.width = "100%";
      document.body.style.height = "100%";
      document.documentElement.style.width = "100%";
      document.documentElement.style.height = "100%";
      if (cel) {
        cel.style.position = "absolute";
        var pad = unpackPadding(sizing.padding);
        cel.style.top = pad.top + "px";
        cel.style.right = pad.right + "px";
        cel.style.bottom = pad.bottom + "px";
        cel.style.left = pad.left + "px";
        el.style.width = "100%";
        el.style.height = "100%";
      }

      return {
        getWidth: function() { return cel.offsetWidth; },
        getHeight: function() { return cel.offsetHeight; }
      };

    } else {
      el.style.width = px(sizing.width);
      el.style.height = px(sizing.height);

      return {
        getWidth: function() { return el.offsetWidth; },
        getHeight: function() { return el.offsetHeight; }
      };
    }
  }

  // Default implementations for methods
  var defaults = {
    find: function(scope) {
      return querySelectorAll(scope, "." + this.name);
    },
    renderError: function(el, err) {
      var $el = $(el);

      this.clearError(el);

      // Add all these error classes, as Shiny does
      var errClass = "shiny-output-error";
      if (err.type !== null) {
        // use the classes of the error condition as CSS class names
        errClass = errClass + " " + $.map(asArray(err.type), function(type) {
          return errClass + "-" + type;
        }).join(" ");
      }
      errClass = errClass + " htmlwidgets-error";

      // Is el inline or block? If inline or inline-block, just display:none it
      // and add an inline error.
      var display = $el.css("display");
      $el.data("restore-display-mode", display);

      if (display === "inline" || display === "inline-block") {
        $el.hide();
        if (err.message !== "") {
          var errorSpan = $("<span>").addClass(errClass);
          errorSpan.text(err.message);
          $el.after(errorSpan);
        }
      } else if (display === "block") {
        // If block, add an error just after the el, set visibility:none on the
        // el, and position the error to be on top of the el.
        // Mark it with a unique ID and CSS class so we can remove it later.
        $el.css("visibility", "hidden");
        if (err.message !== "") {
          var errorDiv = $("<div>").addClass(errClass).css("position", "absolute")
            .css("top", el.offsetTop)
            .css("left", el.offsetLeft)
            // setting width can push out the page size, forcing otherwise
            // unnecessary scrollbars to appear and making it impossible for
            // the element to shrink; so use max-width instead
            .css("maxWidth", el.offsetWidth)
            .css("height", el.offsetHeight);
          errorDiv.text(err.message);
          $el.after(errorDiv);

          // Really dumb way to keep the size/position of the error in sync with
          // the parent element as the window is resized or whatever.
          var intId = setInterval(function() {
            if (!errorDiv[0].parentElement) {
              clearInterval(intId);
              return;
            }
            errorDiv
              .css("top", el.offsetTop)
              .css("left", el.offsetLeft)
              .css("maxWidth", el.offsetWidth)
              .css("height", el.offsetHeight);
          }, 500);
        }
      }
    },
    clearError: function(el) {
      var $el = $(el);
      var display = $el.data("restore-display-mode");
      $el.data("restore-display-mode", null);

      if (display === "inline" || display === "inline-block") {
        if (display)
          $el.css("display", display);
        $(el.nextSibling).filter(".htmlwidgets-error").remove();
      } else if (display === "block"){
        $el.css("visibility", "inherit");
        $(el.nextSibling).filter(".htmlwidgets-error").remove();
      }
    },
    sizing: {}
  };

  // Called by widget bindings to register a new type of widget. The definition
  // object can contain the following properties:
  // - name (required) - A string indicating the binding name, which will be
  //   used by default as the CSS classname to look for.
  // - initialize (optional) - A function(el) that will be called once per
  //   widget element; if a value is returned, it will be passed as the third
  //   value to renderValue.
  // - renderValue (required) - A function(el, data, initValue) that will be
  //   called with data. Static contexts will cause this to be called once per
  //   element; Shiny apps will cause this to be called multiple times per
  //   element, as the data changes.
  window.HTMLWidgets.widget = function(definition) {
    if (!definition.name) {
      throw new Error("Widget must have a name");
    }
    if (!definition.type) {
      throw new Error("Widget must have a type");
    }
    // Currently we only support output widgets
    if (definition.type !== "output") {
      throw new Error("Unrecognized widget type '" + definition.type + "'");
    }
    // TODO: Verify that .name is a valid CSS classname

    // Support new-style instance-bound definitions. Old-style class-bound
    // definitions have one widget "object" per widget per type/class of
    // widget; the renderValue and resize methods on such widget objects
    // take el and instance arguments, because the widget object can't
    // store them. New-style instance-bound definitions have one widget
    // object per widget instance; the definition that's passed in doesn't
    // provide renderValue or resize methods at all, just the single method
    //   factory(el, width, height)
    // which returns an object that has renderValue(x) and resize(w, h).
    // This enables a far more natural programming style for the widget
    // author, who can store per-instance state using either OO-style
    // instance fields or functional-style closure variables (I guess this
    // is in contrast to what can only be called C-style pseudo-OO which is
    // what we required before).
    if (definition.factory) {
      definition = createLegacyDefinitionAdapter(definition);
    }

    if (!definition.renderValue) {
      throw new Error("Widget must have a renderValue function");
    }

    // For static rendering (non-Shiny), use a simple widget registration
    // scheme. We also use this scheme for Shiny apps/documents that also
    // contain static widgets.
    window.HTMLWidgets.widgets = window.HTMLWidgets.widgets || [];
    // Merge defaults into the definition; don't mutate the original definition.
    var staticBinding = extend({}, defaults, definition);
    overrideMethod(staticBinding, "find", function(superfunc) {
      return function(scope) {
        var results = superfunc(scope);
        // Filter out Shiny outputs, we only want the static kind
        return filterByClass(results, "html-widget-output", false);
      };
    });
    window.HTMLWidgets.widgets.push(staticBinding);

    if (shinyMode) {
      // Shiny is running. Register the definition with an output binding.
      // The definition itself will not be the output binding, instead
      // we will make an output binding object that delegates to the
      // definition. This is because we foolishly used the same method
      // name (renderValue) for htmlwidgets definition and Shiny bindings
      // but they actually have quite different semantics (the Shiny
      // bindings receive data that includes lots of metadata that it
      // strips off before calling htmlwidgets renderValue). We can't
      // just ignore the difference because in some widgets it's helpful
      // to call this.renderValue() from inside of resize(), and if
      // we're not delegating, then that call will go to the Shiny
      // version instead of the htmlwidgets version.

      // Merge defaults with definition, without mutating either.
      var bindingDef = extend({}, defaults, definition);

      // This object will be our actual Shiny binding.
      var shinyBinding = new Shiny.OutputBinding();

      // With a few exceptions, we'll want to simply use the bindingDef's
      // version of methods if they are available, otherwise fall back to
      // Shiny's defaults. NOTE: If Shiny's output bindings gain additional
      // methods in the future, and we want them to be overrideable by
      // HTMLWidget binding definitions, then we'll need to add them to this
      // list.
      delegateMethod(shinyBinding, bindingDef, "getId");
      delegateMethod(shinyBinding, bindingDef, "onValueChange");
      delegateMethod(shinyBinding, bindingDef, "onValueError");
      delegateMethod(shinyBinding, bindingDef, "renderError");
      delegateMethod(shinyBinding, bindingDef, "clearError");
      delegateMethod(shinyBinding, bindingDef, "showProgress");

      // The find, renderValue, and resize are handled differently, because we
      // want to actually decorate the behavior of the bindingDef methods.

      shinyBinding.find = function(scope) {
        var results = bindingDef.find(scope);

        // Only return elements that are Shiny outputs, not static ones
        var dynamicResults = results.filter(".html-widget-output");

        // It's possible that whatever caused Shiny to think there might be
        // new dynamic outputs, also caused there to be new static outputs.
        // Since there might be lots of different htmlwidgets bindings, we
        // schedule execution for later--no need to staticRender multiple
        // times.
        if (results.length !== dynamicResults.length)
          scheduleStaticRender();

        return dynamicResults;
      };

      // Wrap renderValue to handle initialization, which unfortunately isn't
      // supported natively by Shiny at the time of this writing.

      shinyBinding.renderValue = function(el, data) {
        Shiny.renderDependencies(data.deps);
        // Resolve strings marked as javascript literals to objects
        if (!(data.evals instanceof Array)) data.evals = [data.evals];
        for (var i = 0; data.evals && i < data.evals.length; i++) {
          window.HTMLWidgets.evaluateStringMember(data.x, data.evals[i]);
        }
        if (!bindingDef.renderOnNullValue) {
          if (data.x === null) {
            el.style.visibility = "hidden";
            return;
          } else {
            el.style.visibility = "inherit";
          }
        }
        if (!elementData(el, "initialized")) {
          initSizing(el);

          elementData(el, "initialized", true);
          if (bindingDef.initialize) {
            var result = bindingDef.initialize(el, el.offsetWidth,
              el.offsetHeight);
            elementData(el, "init_result", result);
          }
        }
        bindingDef.renderValue(el, data.x, elementData(el, "init_result"));
        evalAndRun(data.jsHooks.render, elementData(el, "init_result"), [el, data.x]);
      };

      // Only override resize if bindingDef implements it
      if (bindingDef.resize) {
        shinyBinding.resize = function(el, width, height) {
          // Shiny can call resize before initialize/renderValue have been
          // called, which doesn't make sense for widgets.
          if (elementData(el, "initialized")) {
            bindingDef.resize(el, width, height, elementData(el, "init_result"));
          }
        };
      }

      Shiny.outputBindings.register(shinyBinding, bindingDef.name);
    }
  };

  var scheduleStaticRenderTimerId = null;
  function scheduleStaticRender() {
    if (!scheduleStaticRenderTimerId) {
      scheduleStaticRenderTimerId = setTimeout(function() {
        scheduleStaticRenderTimerId = null;
        window.HTMLWidgets.staticRender();
      }, 1);
    }
  }

  // Render static widgets after the document finishes loading
  // Statically render all elements that are of this widget's class
  window.HTMLWidgets.staticRender = function() {
    var bindings = window.HTMLWidgets.widgets || [];
    forEach(bindings, function(binding) {
      var matches = binding.find(document.documentElement);
      forEach(matches, function(el) {
        var sizeObj = initSizing(el, binding);

        if (hasClass(el, "html-widget-static-bound"))
          return;
        el.className = el.className + " html-widget-static-bound";

        var initResult;
        if (binding.initialize) {
          initResult = binding.initialize(el,
            sizeObj ? sizeObj.getWidth() : el.offsetWidth,
            sizeObj ? sizeObj.getHeight() : el.offsetHeight
          );
          elementData(el, "init_result", initResult);
        }

        if (binding.resize) {
          var lastSize = {
            w: sizeObj ? sizeObj.getWidth() : el.offsetWidth,
            h: sizeObj ? sizeObj.getHeight() : el.offsetHeight
          };
          var resizeHandler = function(e) {
            var size = {
              w: sizeObj ? sizeObj.getWidth() : el.offsetWidth,
              h: sizeObj ? sizeObj.getHeight() : el.offsetHeight
            };
            if (size.w === 0 && size.h === 0)
              return;
            if (size.w === lastSize.w && size.h === lastSize.h)
              return;
            lastSize = size;
            binding.resize(el, size.w, size.h, initResult);
          };

          on(window, "resize", resizeHandler);

          // This is needed for cases where we're running in a Shiny
          // app, but the widget itself is not a Shiny output, but
          // rather a simple static widget. One example of this is
          // an rmarkdown document that has runtime:shiny and widget
          // that isn't in a render function. Shiny only knows to
          // call resize handlers for Shiny outputs, not for static
          // widgets, so we do it ourselves.
          if (window.jQuery) {
            window.jQuery(document).on(
              "shown.htmlwidgets shown.bs.tab.htmlwidgets shown.bs.collapse.htmlwidgets",
              resizeHandler
            );
            window.jQuery(document).on(
              "hidden.htmlwidgets hidden.bs.tab.htmlwidgets hidden.bs.collapse.htmlwidgets",
              resizeHandler
            );
          }

          // This is needed for the specific case of ioslides, which
          // flips slides between display:none and display:block.
          // Ideally we would not have to have ioslide-specific code
          // here, but rather have ioslides raise a generic event,
          // but the rmarkdown package just went to CRAN so the
          // window to getting that fixed may be long.
          if (window.addEventListener) {
            // It's OK to limit this to window.addEventListener
            // browsers because ioslides itself only supports
            // such browsers.
            on(document, "slideenter", resizeHandler);
            on(document, "slideleave", resizeHandler);
          }
        }

        var scriptData = document.querySelector("script[data-for='" + el.id + "'][type='application/json']");
        if (scriptData) {
          var data = JSON.parse(scriptData.textContent || scriptData.text);
          // Resolve strings marked as javascript literals to objects
          if (!(data.evals instanceof Array)) data.evals = [data.evals];
          for (var k = 0; data.evals && k < data.evals.length; k++) {
            window.HTMLWidgets.evaluateStringMember(data.x, data.evals[k]);
          }
          binding.renderValue(el, data.x, initResult);
          evalAndRun(data.jsHooks.render, initResult, [el, data.x]);
        }
      });
    });

    invokePostRenderHandlers();
  }


  function has_jQuery3() {
    if (!window.jQuery) {
      return false;
    }
    var $version = window.jQuery.fn.jquery;
    var $major_version = parseInt($version.split(".")[0]);
    return $major_version >= 3;
  }

  /*
  / Shiny 1.4 bumped jQuery from 1.x to 3.x which means jQuery's
  / on-ready handler (i.e., $(fn)) is now asyncronous (i.e., it now
  / really means $(setTimeout(fn)).
  / https://jquery.com/upgrade-guide/3.0/#breaking-change-document-ready-handlers-are-now-asynchronous
  /
  / Since Shiny uses $() to schedule initShiny, shiny>=1.4 calls initShiny
  / one tick later than it did before, which means staticRender() is
  / called renderValue() earlier than (advanced) widget authors might be expecting.
  / https://github.com/rstudio/shiny/issues/2630
  /
  / For a concrete example, leaflet has some methods (e.g., updateBounds)
  / which reference Shiny methods registered in initShiny (e.g., setInputValue).
  / Since leaflet is privy to this life-cycle, it knows to use setTimeout() to
  / delay execution of those methods (until Shiny methods are ready)
  / https://github.com/rstudio/leaflet/blob/18ec981/javascript/src/index.js#L266-L268
  /
  / Ideally widget authors wouldn't need to use this setTimeout() hack that
  / leaflet uses to call Shiny methods on a staticRender(). In the long run,
  / the logic initShiny should be broken up so that method registration happens
  / right away, but binding happens later.
  */
  function maybeStaticRenderLater() {
    if (shinyMode && has_jQuery3()) {
      window.jQuery(window.HTMLWidgets.staticRender);
    } else {
      window.HTMLWidgets.staticRender();
    }
  }

  if (document.addEventListener) {
    document.addEventListener("DOMContentLoaded", function() {
      document.removeEventListener("DOMContentLoaded", arguments.callee, false);
      maybeStaticRenderLater();
    }, false);
  } else if (document.attachEvent) {
    document.attachEvent("onreadystatechange", function() {
      if (document.readyState === "complete") {
        document.detachEvent("onreadystatechange", arguments.callee);
        maybeStaticRenderLater();
      }
    });
  }


  window.HTMLWidgets.getAttachmentUrl = function(depname, key) {
    // If no key, default to the first item
    if (typeof(key) === "undefined")
      key = 1;

    var link = document.getElementById(depname + "-" + key + "-attachment");
    if (!link) {
      throw new Error("Attachment " + depname + "/" + key + " not found in document");
    }
    return link.getAttribute("href");
  };

  window.HTMLWidgets.dataframeToD3 = function(df) {
    var names = [];
    var length;
    for (var name in df) {
        if (df.hasOwnProperty(name))
            names.push(name);
        if (typeof(df[name]) !== "object" || typeof(df[name].length) === "undefined") {
            throw new Error("All fields must be arrays");
        } else if (typeof(length) !== "undefined" && length !== df[name].length) {
            throw new Error("All fields must be arrays of the same length");
        }
        length = df[name].length;
    }
    var results = [];
    var item;
    for (var row = 0; row < length; row++) {
        item = {};
        for (var col = 0; col < names.length; col++) {
            item[names[col]] = df[names[col]][row];
        }
        results.push(item);
    }
    return results;
  };

  window.HTMLWidgets.transposeArray2D = function(array) {
      if (array.length === 0) return array;
      var newArray = array[0].map(function(col, i) {
          return array.map(function(row) {
              return row[i]
          })
      });
      return newArray;
  };
  // Split value at splitChar, but allow splitChar to be escaped
  // using escapeChar. Any other characters escaped by escapeChar
  // will be included as usual (including escapeChar itself).
  function splitWithEscape(value, splitChar, escapeChar) {
    var results = [];
    var escapeMode = false;
    var currentResult = "";
    for (var pos = 0; pos < value.length; pos++) {
      if (!escapeMode) {
        if (value[pos] === splitChar) {
          results.push(currentResult);
          currentResult = "";
        } else if (value[pos] === escapeChar) {
          escapeMode = true;
        } else {
          currentResult += value[pos];
        }
      } else {
        currentResult += value[pos];
        escapeMode = false;
      }
    }
    if (currentResult !== "") {
      results.push(currentResult);
    }
    return results;
  }
  // Function authored by Yihui/JJ Allaire
  window.HTMLWidgets.evaluateStringMember = function(o, member) {
    var parts = splitWithEscape(member, '.', '\\');
    for (var i = 0, l = parts.length; i < l; i++) {
      var part = parts[i];
      // part may be a character or 'numeric' member name
      if (o !== null && typeof o === "object" && part in o) {
        if (i == (l - 1)) { // if we are at the end of the line then evalulate
          if (typeof o[part] === "string")
            o[part] = tryEval(o[part]);
        } else { // otherwise continue to next embedded object
          o = o[part];
        }
      }
    }
  };

  // Retrieve the HTMLWidget instance (i.e. the return value of an
  // HTMLWidget binding's initialize() or factory() function)
  // associated with an element, or null if none.
  window.HTMLWidgets.getInstance = function(el) {
    return elementData(el, "init_result");
  };

  // Finds the first element in the scope that matches the selector,
  // and returns the HTMLWidget instance (i.e. the return value of
  // an HTMLWidget binding's initialize() or factory() function)
  // associated with that element, if any. If no element matches the
  // selector, or the first matching element has no HTMLWidget
  // instance associated with it, then null is returned.
  //
  // The scope argument is optional, and defaults to window.document.
  window.HTMLWidgets.find = function(scope, selector) {
    if (arguments.length == 1) {
      selector = scope;
      scope = document;
    }

    var el = scope.querySelector(selector);
    if (el === null) {
      return null;
    } else {
      return window.HTMLWidgets.getInstance(el);
    }
  };

  // Finds all elements in the scope that match the selector, and
  // returns the HTMLWidget instances (i.e. the return values of
  // an HTMLWidget binding's initialize() or factory() function)
  // associated with the elements, in an array. If elements that
  // match the selector don't have an associated HTMLWidget
  // instance, the returned array will contain nulls.
  //
  // The scope argument is optional, and defaults to window.document.
  window.HTMLWidgets.findAll = function(scope, selector) {
    if (arguments.length == 1) {
      selector = scope;
      scope = document;
    }

    var nodes = scope.querySelectorAll(selector);
    var results = [];
    for (var i = 0; i < nodes.length; i++) {
      results.push(window.HTMLWidgets.getInstance(nodes[i]));
    }
    return results;
  };

  var postRenderHandlers = [];
  function invokePostRenderHandlers() {
    while (postRenderHandlers.length) {
      var handler = postRenderHandlers.shift();
      if (handler) {
        handler();
      }
    }
  }

  // Register the given callback function to be invoked after the
  // next time static widgets are rendered.
  window.HTMLWidgets.addPostRenderHandler = function(callback) {
    postRenderHandlers.push(callback);
  };

  // Takes a new-style instance-bound definition, and returns an
  // old-style class-bound definition. This saves us from having
  // to rewrite all the logic in this file to accomodate both
  // types of definitions.
  function createLegacyDefinitionAdapter(defn) {
    var result = {
      name: defn.name,
      type: defn.type,
      initialize: function(el, width, height) {
        return defn.factory(el, width, height);
      },
      renderValue: function(el, x, instance) {
        return instance.renderValue(x);
      },
      resize: function(el, width, height, instance) {
        return instance.resize(width, height);
      }
    };

    if (defn.find)
      result.find = defn.find;
    if (defn.renderError)
      result.renderError = defn.renderError;
    if (defn.clearError)
      result.clearError = defn.clearError;

    return result;
  }
})();

</script>
<script src="https://d3js.org/d3.v5.min.js"></script>
<script>!function(t,n){"object"==typeof exports&&"undefined"!=typeof module?n(exports,require("d3-selection"),require("d3-drag")):"function"==typeof define&&define.amd?define(["exports","d3-selection","d3-drag"],n):n(t.d3=t.d3||{},t.d3,t.d3)}(this,function(t,n,r){"use strict";function e(t,n){return n={exports:{}},t(n,n.exports),n.exports}function o(){function t(t){function u(){p=[],h="",_.attr("d",null),m.attr("d",null),r.nodes().forEach(function(t){t.__lasso.possible=!1,t.__lasso.selected=!1,t.__lasso.hoverSelect=!1,t.__lasso.loopSelect=!1;var n=t.getBoundingClientRect();t.__lasso.lassoPoint=[Math.round(n.left+n.width/2),Math.round(n.top+n.height/2)]}),s&&r.on("mouseover.lasso",function(){this.__lasso.hoverSelect=!0}),i.start()}function l(){var t,n;"touchmove"===d3.event.sourceEvent.type?(t=d3.event.sourceEvent.touches[0].clientX,n=d3.event.sourceEvent.touches[0].clientY):(t=d3.event.sourceEvent.clientX,n=d3.event.sourceEvent.clientY);var s=d3.mouse(this)[0],u=d3.mouse(this)[1];""===h?(h=h+"M "+s+" "+u,v=[t,n],d=[s,u],b.attr("cx",s).attr("cy",u).attr("r",7).attr("display",null)):h=h+" L "+s+" "+u,p.push([t,n]);var l=Math.sqrt(Math.pow(t-v[0],2)+Math.pow(n-v[1],2)),f="M "+s+" "+u+" L "+d[0]+" "+d[1];_.attr("d",h),m.attr("d",f),a=l<=e,a&&o?m.attr("display",null):m.attr("display","none"),r.nodes().forEach(function(t){t.__lasso.loopSelect=!(!a||!o)&&c(p,t.__lasso.lassoPoint)<1,t.__lasso.possible=t.__lasso.hoverSelect||t.__lasso.loopSelect}),i.draw()}function f(){r.on("mouseover.lasso",null),r.nodes().forEach(function(t){t.__lasso.selected=t.__lasso.possible,t.__lasso.possible=!1}),_.attr("d",null),m.attr("d",null),b.attr("display","none"),i.end()}var h,v,d,p,g=t.append("g").attr("class","lasso"),_=g.append("path").attr("class","drawn"),m=g.append("path").attr("class","loop_close"),b=g.append("circle").attr("class","origin"),M=d3.drag().on("start",u).on("drag",l).on("end",f);n.call(M)}var n,r=[],e=75,o=!0,a=!1,s=!0,i={start:function(){},draw:function(){},end:function(){}};return t.items=function(n){if(!arguments.length)return r;r=n;var e=r.nodes();return e.forEach(function(t){t.__lasso={possible:!1,selected:!1}}),t},t.possibleItems=function(){return r.filter(function(){return this.__lasso.possible})},t.selectedItems=function(){return r.filter(function(){return this.__lasso.selected})},t.notPossibleItems=function(){return r.filter(function(){return!this.__lasso.possible})},t.notSelectedItems=function(){return r.filter(function(){return!this.__lasso.selected})},t.closePathDistance=function(n){return arguments.length?(e=n,t):e},t.closePathSelect=function(n){return arguments.length?(o=n===!0,t):o},t.isPathClosed=function(n){return arguments.length?(a=n===!0,t):a},t.hoverSelect=function(n){return arguments.length?(s=n===!0,t):s},t.on=function(n,r){if(!arguments.length)return i;if(1===arguments.length)return i[n];var e=["start","draw","end"];return e.indexOf(n)>-1&&(i[n]=r),t},t.targetArea=function(r){return arguments.length?(n=r,t):n},t}var a=e(function(t){function n(t,n,e){var o=t*n,a=r*t,s=a-t,i=a-s,u=t-i,l=r*n,f=l-n,c=l-f,h=n-c,v=o-i*c,d=v-u*c,p=d-i*h,g=u*h-p;return e?(e[0]=g,e[1]=o,e):[g,o]}t.exports=n;var r=+(Math.pow(2,27)+1)}),s=e(function(t){function n(t,n){var r=t+n,e=r-t,o=r-e,a=n-e,s=t-o,i=s+a;return i?[i,r]:[r]}function r(t,r){var e=0|t.length,o=0|r.length;if(1===e&&1===o)return n(t[0],r[0]);var a,s,i=e+o,u=new Array(i),l=0,f=0,c=0,h=Math.abs,v=t[f],d=h(v),p=r[c],g=h(p);d<g?(s=v,f+=1,f<e&&(v=t[f],d=h(v))):(s=p,c+=1,c<o&&(p=r[c],g=h(p))),f<e&&d<g||c>=o?(a=v,f+=1,f<e&&(v=t[f],d=h(v))):(a=p,c+=1,c<o&&(p=r[c],g=h(p)));for(var _,m,b,M,y,w=a+s,x=w-a,j=s-x,E=j,A=w;f<e&&c<o;)d<g?(a=v,f+=1,f<e&&(v=t[f],d=h(v))):(a=p,c+=1,c<o&&(p=r[c],g=h(p))),s=E,w=a+s,x=w-a,j=s-x,j&&(u[l++]=j),_=A+w,m=_-A,b=_-m,M=w-m,y=A-b,E=y+M,A=_;for(;f<e;)a=v,s=E,w=a+s,x=w-a,j=s-x,j&&(u[l++]=j),_=A+w,m=_-A,b=_-m,M=w-m,y=A-b,E=y+M,A=_,f+=1,f<e&&(v=t[f]);for(;c<o;)a=p,s=E,w=a+s,x=w-a,j=s-x,j&&(u[l++]=j),_=A+w,m=_-A,b=_-m,M=w-m,y=A-b,E=y+M,A=_,c+=1,c<o&&(p=r[c]);return E&&(u[l++]=E),A&&(u[l++]=A),l||(u[l++]=0),u.length=l,u}t.exports=r}),i=e(function(t){function n(t,n,r){var e=t+n,o=e-t,a=e-o,s=n-o,i=t-a;return r?(r[0]=i+s,r[1]=e,r):[i+s,e]}t.exports=n}),u=e(function(t){function n(t,n){var o=t.length;if(1===o){var a=r(t[0],n);return a[0]?a:[a[1]]}var s=new Array(2*o),i=[.1,.1],u=[.1,.1],l=0;r(t[0],n,i),i[0]&&(s[l++]=i[0]);for(var f=1;f<o;++f){r(t[f],n,u);var c=i[1];e(c,u[0],i),i[0]&&(s[l++]=i[0]);var h=u[1],v=i[1],d=h+v,p=d-h,g=v-p;i[1]=d,g&&(s[l++]=g)}return i[1]&&(s[l++]=i[1]),0===l&&(s[l++]=0),s.length=l,s}var r=a,e=i;t.exports=n}),l=e(function(t){function n(t,n){var r=t+n,e=r-t,o=r-e,a=n-e,s=t-o,i=s+a;return i?[i,r]:[r]}function r(t,r){var e=0|t.length,o=0|r.length;if(1===e&&1===o)return n(t[0],-r[0]);var a,s,i=e+o,u=new Array(i),l=0,f=0,c=0,h=Math.abs,v=t[f],d=h(v),p=-r[c],g=h(p);d<g?(s=v,f+=1,f<e&&(v=t[f],d=h(v))):(s=p,c+=1,c<o&&(p=-r[c],g=h(p))),f<e&&d<g||c>=o?(a=v,f+=1,f<e&&(v=t[f],d=h(v))):(a=p,c+=1,c<o&&(p=-r[c],g=h(p)));for(var _,m,b,M,y,w=a+s,x=w-a,j=s-x,E=j,A=w;f<e&&c<o;)d<g?(a=v,f+=1,f<e&&(v=t[f],d=h(v))):(a=p,c+=1,c<o&&(p=-r[c],g=h(p))),s=E,w=a+s,x=w-a,j=s-x,j&&(u[l++]=j),_=A+w,m=_-A,b=_-m,M=w-m,y=A-b,E=y+M,A=_;for(;f<e;)a=v,s=E,w=a+s,x=w-a,j=s-x,j&&(u[l++]=j),_=A+w,m=_-A,b=_-m,M=w-m,y=A-b,E=y+M,A=_,f+=1,f<e&&(v=t[f]);for(;c<o;)a=p,s=E,w=a+s,x=w-a,j=s-x,j&&(u[l++]=j),_=A+w,m=_-A,b=_-m,M=w-m,y=A-b,E=y+M,A=_,c+=1,c<o&&(p=-r[c]);return E&&(u[l++]=E),A&&(u[l++]=A),l||(u[l++]=0),u.length=l,u}t.exports=r}),f=e(function(t){function n(t,n){for(var r=new Array(t.length-1),e=1;e<t.length;++e)for(var o=r[e-1]=new Array(t.length-1),a=0,s=0;a<t.length;++a)a!==n&&(o[s++]=t[e][a]);return r}function r(t){for(var n=new Array(t),r=0;r<t;++r){n[r]=new Array(t);for(var e=0;e<t;++e)n[r][e]=["m",e,"[",t-r-1,"]"].join("")}return n}function e(t){return 1&t?"-":""}function o(t){if(1===t.length)return t[0];if(2===t.length)return["sum(",t[0],",",t[1],")"].join("");var n=t.length>>1;return["sum(",o(t.slice(0,n)),",",o(t.slice(n)),")"].join("")}function i(t){if(2===t.length)return[["sum(prod(",t[0][0],",",t[1][1],"),prod(-",t[0][1],",",t[1][0],"))"].join("")];for(var r=[],a=0;a<t.length;++a)r.push(["scale(",o(i(n(t,a))),",",e(a),t[0][a],")"].join(""));return r}function f(t){for(var e=[],a=[],s=r(t),u=[],l=0;l<t;++l)0===(1&l)?e.push.apply(e,i(n(s,l))):a.push.apply(a,i(n(s,l))),u.push("m"+l);var f=o(e),c=o(a),h="orientation"+t+"Exact",_=["function ",h,"(",u.join(),"){var p=",f,",n=",c,",d=sub(p,n);return d[d.length-1];};return ",h].join(""),m=new Function("sum","prod","scale","sub",_);return m(d,v,p,g)}function c(t){var n=x[t.length];return n||(n=x[t.length]=f(t.length)),n.apply(void 0,t)}function h(){for(;x.length<=_;)x.push(f(x.length));for(var n=[],r=["slow"],e=0;e<=_;++e)n.push("a"+e),r.push("o"+e);for(var o=["function getOrientation(",n.join(),"){switch(arguments.length){case 0:case 1:return 0;"],e=2;e<=_;++e)o.push("case ",e,":return o",e,"(",n.slice(0,e).join(),");");o.push("}var s=new Array(arguments.length);for(var i=0;i<arguments.length;++i){s[i]=arguments[i]};return slow(s);}return getOrientation"),r.push(o.join(""));var a=Function.apply(void 0,r);t.exports=a.apply(void 0,[c].concat(x));for(var e=0;e<=_;++e)t.exports[e]=x[e]}var v=a,d=s,p=u,g=l,_=5,m=1.1102230246251565e-16,b=(3+16*m)*m,M=(7+56*m)*m,y=f(3),w=f(4),x=[function(){return 0},function(){return 0},function(t,n){return n[0]-t[0]},function(t,n,r){var e,o=(t[1]-r[1])*(n[0]-r[0]),a=(t[0]-r[0])*(n[1]-r[1]),s=o-a;if(o>0){if(a<=0)return s;e=o+a}else{if(!(o<0))return s;if(a>=0)return s;e=-(o+a)}var i=b*e;return s>=i||s<=-i?s:y(t,n,r)},function(t,n,r,e){var o=t[0]-e[0],a=n[0]-e[0],s=r[0]-e[0],i=t[1]-e[1],u=n[1]-e[1],l=r[1]-e[1],f=t[2]-e[2],c=n[2]-e[2],h=r[2]-e[2],v=a*l,d=s*u,p=s*i,g=o*l,_=o*u,m=a*i,b=f*(v-d)+c*(p-g)+h*(_-m),y=(Math.abs(v)+Math.abs(d))*Math.abs(f)+(Math.abs(p)+Math.abs(g))*Math.abs(c)+(Math.abs(_)+Math.abs(m))*Math.abs(h),x=M*y;return b>x||-b>x?b:w(t,n,r,e)}];h()}),c=e(function(t){function n(t,n){for(var e=n[0],o=n[1],a=t.length,s=1,i=a,u=0,l=a-1;u<i;l=u++){var f=t[u],c=t[l],h=f[1],v=c[1];if(v<h){if(v<o&&o<h){var d=r(f,c,n);if(0===d)return 0;s^=0<d|0}else if(o===h){var p=t[(u+1)%a],g=p[1];if(h<g){var d=r(f,c,n);if(0===d)return 0;s^=0<d|0}}}else if(h<v){if(h<o&&o<v){var d=r(f,c,n);if(0===d)return 0;s^=d<0|0}else if(o===h){var p=t[(u+1)%a],g=p[1];if(g<h){var d=r(f,c,n);if(0===d)return 0;s^=d<0|0}}}else if(o===h){var _=Math.min(f[0],c[0]),m=Math.max(f[0],c[0]);if(0===u){for(;l>0;){var b=(l+a-1)%a,M=t[b];if(M[1]!==o)break;var y=M[0];_=Math.min(_,y),m=Math.max(m,y),l=b}if(0===l)return _<=e&&e<=m?0:1;i=l+1}for(var w=t[(l+a-1)%a][1];u+1<i;){var M=t[u+1];if(M[1]!==o)break;var y=M[0];_=Math.min(_,y),m=Math.max(m,y),u+=1}if(_<=e&&e<=m)return 0;var x=t[(u+1)%a][1];e<_&&w<o!=x<o&&(s^=1)}}return 2*s-1}t.exports=n;var r=f});t.lasso=o,Object.defineProperty(t,"__esModule",{value:!0})});</script>
<style type="text/css">.girafe {
margin-left: auto;
margin-right: auto;
}
.girafe_container_std {
margin-left: auto;
margin-right: auto;
position: relative;
text-align: center;
}
.girafe_container_ie {
height: 0;
}
.girafe_svg_ie {
position: absolute;
top: 0;
left: 0;
}
.ggiraph-toolbar {
position: absolute;
background-color: rgb(255, 255, 255);
background-color: rgba(255, 255, 255,.3);
border-radius: 5px;
padding-top:2px;
opacity: 0;
transition: opacity 0.3s ease 0s;
}
.ggiraph-toolbar-top {
text-align:center;
top: 3px;
right: 0px;
left: 0px;
}
.ggiraph-toolbar-topleft {
text-align:left;
top: 3px;
left: 3px;
}
.ggiraph-toolbar-topright {
text-align:right;
top: 3px;
right: 3px;
}
.ggiraph-toolbar-bottom {
text-align:center;
bottom: 3px;
right: 0px;
left: 0px;
}
.ggiraph-toolbar-bottomleft {
text-align:left;
left: 3px;
bottom: 3px;
}
.ggiraph-toolbar-bottomright {
text-align:right;
right: 3px;
bottom: 3px;
}
.ggiraph-toolbar-block {
display: inline-block;
padding-right: 10px;
}
.ggiraph-toolbar-icon {
position: relative;
cursor: pointer;
box-sizing: border-box;
padding-right: 3px;
}
.drop {
stroke: #E30C37;
fill: #E30C37;
}
.drop:hover{
stroke: #333333;
fill: #333333;
}
.neutral {
stroke: #006699;
fill: #006699;
}
.neutral:hover{
stroke: #333333;
fill: #333333;
}
.lasso path {
stroke: rgb(80,80,80);
stroke-width:2px;
}
.lasso .drawn {
fill-opacity:.05 ;
}
.lasso .loop_close {
fill:none;
stroke-dasharray: 4,4;
}
.lasso .origin {
fill:#333333;
fill-opacity:.5;
}
</style>
<script>var ggiraphjs=function(e){var t={};function n(i){if(t[i])return t[i].exports;var r=t[i]={i:i,l:!1,exports:{}};return e[i].call(r.exports,r,r.exports,n),r.l=!0,r.exports}return n.m=e,n.c=t,n.d=function(e,t,i){n.o(e,t)||Object.defineProperty(e,t,{enumerable:!0,get:i})},n.r=function(e){"undefined"!=typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},n.t=function(e,t){if(1&t&&(e=n(e)),8&t)return e;if(4&t&&"object"==typeof e&&e&&e.__esModule)return e;var i=Object.create(null);if(n.r(i),Object.defineProperty(i,"default",{enumerable:!0,value:e}),2&t&&"string"!=typeof e)for(var r in e)n.d(i,r,function(t){return e[t]}.bind(null,r));return i},n.n=function(e){var t=e&&e.__esModule?function(){return e.default}:function(){return e};return n.d(t,"a",t),t},n.o=function(e,t){return Object.prototype.hasOwnProperty.call(e,t)},n.p="",n(n.s=2)}([function(e,t){e.exports=d3},function(e,t,n){"use strict";var i;!function(){var n=void 0!==t&&t||{}||this||window;void 0===(i=function(){return n}.apply(t,[]))||(e.exports=i);var r="http://www.w3.org/2000/xmlns/",s=/url\(["']?(.+?)["']?\)/,o={woff2:"font/woff2",woff:"font/woff",otf:"application/x-font-opentype",ttf:"application/x-font-ttf",eot:"application/vnd.ms-fontobject",sfnt:"application/font-sfnt",svg:"image/svg+xml"},a=function(e){if(!function(e){return e instanceof HTMLElement||e instanceof SVGElement}(e))throw new Error("an HTMLElement or SVGElement is required; got "+e)},l=function(e,t,n){var i=e.viewBox&&e.viewBox.baseVal&&e.viewBox.baseVal[n]||null!==t.getAttribute(n)&&!t.getAttribute(n).match(/%$/)&&parseInt(t.getAttribute(n))||e.getBoundingClientRect()[n]||parseInt(t.style[n])||parseInt(window.getComputedStyle(e).getPropertyValue(n));return void 0===i||null===i||isNaN(parseFloat(i))?0:i},c=function(e){for(var t=window.atob(e.split(",")[1]),n=e.split(",")[0].split(":")[1].split(";")[0],i=new ArrayBuffer(t.length),r=new Uint8Array(i),s=0;s<t.length;s++)r[s]=t.charCodeAt(s);return new Blob([i],{type:n})},d=function(e,t){var n=e.cssText.match(s),i=n&&n[1]||"";if(i&&!i.match(/^data:/)&&"about:blank"!==i){var r=i.startsWith("../")?t+"/../"+i:i.startsWith("./")?t+"/."+i:i;return{text:e.cssText,format:function(e){var t=Object.keys(o).filter(function(t){return e.indexOf("."+t)>0}).map(function(e){return o[e]});return t?t[0]:(console.error("Unknown font format for "+e+". Fonts may not be working correctly."),"application/octet-stream")}(r),url:r}}},u=function(e){return Promise.all(Array.from(e.querySelectorAll("image")).map(function(e){var t=e.getAttributeNS("http://www.w3.org/1999/xlink","href")||e.getAttribute("href");return t?(function(e){return e&&0===e.lastIndexOf("http",0)&&-1===e.lastIndexOf(window.location.host)}(t)&&(t+=(-1===t.indexOf("?")?"?":"&")+"t="+(new Date).valueOf()),new Promise(function(n,i){var r=document.createElement("canvas"),s=new Image;s.crossOrigin="anonymous",s.src=t,s.onerror=function(){return i(new Error("Could not load "+t))},s.onload=function(){r.width=s.width,r.height=s.height,r.getContext("2d").drawImage(s,0,0),e.setAttributeNS("http://www.w3.org/1999/xlink","href",r.toDataURL("image/png")),n(!0)}})):Promise.resolve(null)}))},h={},f=function(e){return Promise.all(e.map(function(e){return new Promise(function(t,n){if(h[e.url])return t(h[e.url]);var i=new XMLHttpRequest;i.addEventListener("load",function(){var n=function(e){for(var t="",n=new Uint8Array(e),i=0;i<n.byteLength;i++)t+=String.fromCharCode(n[i]);return window.btoa(t)}(i.response),r=e.text.replace(s,'url("data:'+e.format+";base64,"+n+'")')+"\n";h[e.url]=r,t(r)}),i.addEventListener("error",function(n){console.warn("Failed to load font from: "+e.url,n),h[e.url]=null,t(null)}),i.addEventListener("abort",function(n){console.warn("Aborted loading font from: "+e.url,n),t(null)}),i.open("GET",e.url),i.responseType="arraybuffer",i.send()})})).then(function(e){return e.filter(function(e){return e}).join("")})},g=null,p=function(e,t){var n=t||{},i=n.selectorRemap,r=n.modifyStyle,s=n.modifyCss,o=n.fonts,a=s||function(e,t){return(i?i(e):e)+"{"+(r?r(t):t)+"}\n"},l=[],c=void 0===o,u=o||[];return(g||(g=Array.from(document.styleSheets).map(function(e){try{return{rules:e.cssRules,href:e.href}}catch(t){return console.warn("Stylesheet could not be loaded: "+e.href,t),{}}}))).forEach(function(t){var n=t.rules,i=t.href;n&&Array.from(n).forEach(function(t){if(void 0!==t.style)if(function(e,t){if(t)try{return e.querySelector(t)||e.parentNode&&e.parentNode.querySelector(t)}catch(e){console.warn('Invalid CSS selector "'+t+'"',e)}}(e,t.selectorText))l.push(a(t.selectorText,t.style.cssText));else if(c&&t.cssText.match(/^@font-face/)){var n=d(t,i);n&&u.push(n)}else l.push(t.cssText)})}),f(u).then(function(e){return l.join("\n")+e})};n.prepareSvg=function(e,t,n){a(e);var i=t||{},s=i.left,o=void 0===s?0:s,c=i.top,d=void 0===c?0:c,h=i.width,f=i.height,g=i.scale,v=void 0===g?1:g,y=i.responsive,m=void 0!==y&&y;return u(e).then(function(){var i=e.cloneNode(!0),s=(t||{}).backgroundColor,a=void 0===s?"transparent":s;i.style.backgroundColor=a;var c=function(e,t,n,i){if("svg"===e.tagName)return{width:n||l(e,t,"width"),height:i||l(e,t,"height")};if(e.getBBox){var r=e.getBBox(),s=r.x,o=r.y;return{width:s+r.width,height:o+r.height}}}(e,i,h,f),u=c.width,g=c.height;if("svg"!==e.tagName){if(!e.getBBox)return void console.error("Attempted to render non-SVG element",e);i.setAttribute("transform",i.getAttribute("transform").replace(/translate\(.*?\)/,""));var y=document.createElementNS("http://www.w3.org/2000/svg","svg");y.appendChild(i),i=y}return i.setAttribute("version","1.1"),i.setAttribute("viewBox",[o,d,u,g].join(" ")),i.getAttribute("xmlns")||i.setAttributeNS(r,"xmlns","http://www.w3.org/2000/svg"),i.getAttribute("xmlns:xlink")||i.setAttributeNS(r,"xmlns:xlink","http://www.w3.org/1999/xlink"),m?(i.removeAttribute("width"),i.removeAttribute("height"),i.setAttribute("preserveAspectRatio","xMinYMin meet")):(i.setAttribute("width",u*v),i.setAttribute("height",g*v)),Array.from(i.querySelectorAll("foreignObject > *")).forEach(function(e){e.getAttribute("xmlns")||e.setAttributeNS(r,"xmlns","http://www.w3.org/1999/xhtml")}),p(e,t).then(function(e){var t=document.createElement("style");t.setAttribute("type","text/css"),t.innerHTML="<![CDATA[\n"+e+"\n]]>";var r=document.createElement("defs");r.appendChild(t),i.insertBefore(r,i.firstChild);var s=document.createElement("div");s.appendChild(i);var o=s.innerHTML.replace(/NS\d+:href/gi,'xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href');if("function"!=typeof n)return{src:o,width:u,height:g};n(o,u,g)})})},n.svgAsDataUri=function(e,t,i){a(e);var r=n.prepareSvg(e,t).then(function(e){var t=e.src;return"data:image/svg+xml;base64,"+window.btoa(function(e){return decodeURIComponent(encodeURIComponent(e).replace(/%([0-9A-F]{2})/g,function(e,t){var n=String.fromCharCode("0x"+t);return"%"===n?"%25":n}))}('<?xml version="1.0" standalone="no"?><!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd" [<!ENTITY nbsp "&#160;">]>'+t))});return"function"==typeof i?r.then(i):r},n.svgAsPngUri=function(e,t,i){a(e);var r=t||{},s=r.encoderType,o=void 0===s?"image/png":s,l=r.encoderOptions,c=void 0===l?.8:l,d=r.canvg,u=function(e){var t=e.src,n=e.width,r=e.height,s=document.createElement("canvas"),a=s.getContext("2d"),l=window.devicePixelRatio||1;s.width=n*l,s.height=r*l,s.style.width=s.width+"px",s.style.height=s.height+"px",a.setTransform(l,0,0,l,0,0),d?d(s,t):a.drawImage(t,0,0);var u=void 0;try{u=s.toDataURL(o,c)}catch(e){if("undefined"!=typeof SecurityError&&e instanceof SecurityError||"SecurityError"===e.name)return void console.error("Rendered SVG images cannot be downloaded in this browser.");throw e}return"function"==typeof i&&i(u),Promise.resolve(u)};return d?n.prepareSvg(e,t).then(u):n.svgAsDataUri(e,t).then(function(e){return new Promise(function(t,n){var i=new Image;i.onload=function(){return t(u({src:i,width:i.width,height:i.height}))},i.onerror=function(){n("There was an error loading the data URI as an image on the following SVG\n"+window.atob(e.slice(26))+"Open the following link to see browser's diagnosis\n"+e)},i.src=e})})},n.download=function(e,t){if(navigator.msSaveOrOpenBlob)navigator.msSaveOrOpenBlob(c(t),e);else{var n=document.createElement("a");if("download"in n){n.download=e,n.style.display="none",document.body.appendChild(n);try{var i=c(t),r=URL.createObjectURL(i);n.href=r,n.onclick=function(){return requestAnimationFrame(function(){return URL.revokeObjectURL(r)})}}catch(e){console.warn("This browser does not support object URLs. Falling back to string URL."),n.href=t}n.click(),document.body.removeChild(n)}else window.open(t,"_temp","menubar=no,toolbar=no,status=no")}},n.saveSvg=function(e,t,i){a(e),n.svgAsDataUri(e,i||{},function(e){return n.download(t,e)})},n.saveSvgAsPng=function(e,t,i){a(e),n.svgAsPngUri(e,i||{},function(e){return n.download(t,e)})}}()},function(e,t,n){e.exports=n(3)},function(e,t,n){"use strict";n.r(t);var i=n(0),r=n(1);function s(){var e,t=navigator.userAgent,n=t.match(/(opera|chrome|safari|firefox|msie|trident(?=\/))\/?\s*(\d+)/i)||[];return/trident/i.test(n[1])?"IE "+((e=/\brv[ :]+(\d+)/g.exec(t)||[])[1]||""):"Chrome"===n[1]&&null!==(e=t.match(/\b(OPR|Edge)\/(\d+)/))?e.slice(1).join(" ").replace("OPR","Opera"):(n=n[2]?[n[1],n[2]]:[navigator.appName,navigator.appVersion,"-?"],null!==(e=t.match(/version\/(\d+)/i))&&n.splice(1,1,e[1]),n.join(" "))}function o(e,t){for(var n=0;n<t.length;n++){var i=t[n];i.enumerable=i.enumerable||!1,i.configurable=!0,"value"in i&&(i.writable=!0),Object.defineProperty(e,i.key,i)}}var a=function(){function e(t){!function(e,t){if(!(e instanceof t))throw new TypeError("Cannot call a class as a function")}(this,e),this.containerid=t,this.dataSelected=[],this.dataKeySelected=[],this.dataThemeSelected=[],this.typeSelection=null,this.svgid=null,this.inputId=null,this.inputKeyId=null,this.inputThemeId=null,this.zoomer=null}return function(e,t,n){t&&o(e.prototype,t),n&&o(e,n)}(e,[{key:"setSvgId",value:function(e){this.svgid=e}},{key:"setZoomer",value:function(e,t){this.zoomer=i.zoom().scaleExtent([e,t])}},{key:"tooltipClassname",value:function(){return"tooltip_"+this.svgid}},{key:"selectedDataClassname",value:function(){return"selected_"+this.svgid}},{key:"selectedKeyClassname",value:function(){return"selected_key_"+this.svgid}},{key:"selectedThemeClassname",value:function(){return"selected_theme_"+this.svgid}},{key:"hoverClassname",value:function(){return"hover_"+this.svgid}},{key:"hoverKeyClassname",value:function(){return"hover_key_"+this.svgid}},{key:"hoverThemeClassname",value:function(){return"hover_theme_"+this.svgid}},{key:"addStyle",value:function(e,t,n,r,s,o,a){var l=i.select("#"+this.containerid+" style");l.size()>0&&l.remove();var c=e.replace(/SVGID_/g,this.svgid)+"\n"+t.replace(/SVGID_/g,this.svgid)+"\n"+n.replace(/SVGID_/g,this.svgid)+"\n"+r.replace(/SVGID_/g,this.svgid)+"\n"+s.replace(/SVGID_/g,this.svgid)+"\n"+o.replace(/SVGID_/g,this.svgid)+"\n"+a.replace(/SVGID_/g,this.svgid)+"\n";i.select("#"+this.containerid).append("style").text(c)}},{key:"setInputId",value:function(e){this.inputId=e}},{key:"setInputKeyId",value:function(e){this.inputKeyId=e}},{key:"setInputThemeId",value:function(e){this.inputThemeId=e}},{key:"addUI",value:function(e,t,n,i){!function(e,t,n,i,s){var o=d3.select("#"+e.containerid+" .girafe_container_std").append("div").classed("ggiraph-toolbar",!0);if(o.classed(s,!0),t){var a=o.append("div").classed("ggiraph-toolbar-block",!0).classed("shinyonly",!0);a.append("a").classed("ggiraph-toolbar-icon",!0).classed("neutral",!0).attr("title","lasso selection").on("click",function(){e.lasso_on(!0)}).html("<svg width='15pt' height='15pt' viewBox='0 0 230 230'><g><ellipse ry='65.5' rx='86.5' cy='94' cx='115.5' stroke-width='20' fill='transparent'/><ellipse ry='11.500001' rx='10.5' cy='153' cx='91.5' stroke-width='20' fill='transparent'/><line y2='210.5' x2='105' y1='164.5' x1='96' stroke-width='20'/></g></svg>"),a.append("a").classed("ggiraph-toolbar-icon",!0).classed("drop",!0).attr("title","lasso selection").on("click",function(){e.lasso_on(!1)}).html("<svg width='15pt' height='15pt' viewBox='0 0 230 230'><g><ellipse ry='65.5' rx='86.5' cy='94' cx='115.5' stroke-width='20' fill='transparent'/><ellipse ry='11.500001' rx='10.5' cy='153' cx='91.5' stroke-width='20' fill='transparent'/><line y2='210.5' x2='105' y1='164.5' x1='96' stroke-width='20'/></g></svg>")}if(n){var l=o.append("div").classed("ggiraph-toolbar-block",!0);l.append("a").classed("ggiraph-toolbar-icon",!0).classed("neutral",!0).attr("title","pan-zoom reset").on("click",function(){e.zoomIdentity()}).html("<svg width='15pt' height='15pt' viewBox='0 0 512 512'><g><polygon points='274,209.7 337.9,145.9 288,96 416,96 416,224 366.1,174.1 302.3,238 '/><polygon points='274,302.3 337.9,366.1 288,416 416,416 416,288 366.1,337.9 302.3,274'/><polygon points='238,302.3 174.1,366.1 224,416 96,416 96,288 145.9,337.9 209.7,274'/><polygon points='238,209.7 174.1,145.9 224,96 96,96 96,224 145.9,174.1 209.7,238'/></g><svg>"),l.append("a").classed("ggiraph-toolbar-icon",!0).classed("neutral",!0).attr("title","activate pan-zoom").on("click",function(){e.zoomOn()}).html("<svg width='15pt' height='15pt' viewBox='0 0 512 512'><g><ellipse ry='150' rx='150' cy='213' cx='203.5' stroke-width='50' fill='transparent'/><line y2='455.5' x2='416' y1='331.5' x1='301' stroke-width='50'/></g></svg>"),l.append("a").classed("ggiraph-toolbar-icon",!0).classed("drop",!0).attr("title","deactivate pan-zoom").on("click",function(){e.zoomOff()}).html("<svg width='15pt' height='15pt' viewBox='0 0 512 512'><g><ellipse ry='150' rx='150' cy='213' cx='203.5' stroke-width='50' fill='transparent'/><line y2='455.5' x2='416' y1='331.5' x1='301' stroke-width='50'/><line y2='455' x2='0' y1='0' x1='416' stroke-width='30'/></g></svg>")}i&&o.append("div").classed("ggiraph-toolbar-block",!0).append("a").classed("ggiraph-toolbar-icon",!0).classed("neutral",!0).attr("title","download png").on("click",function(){"undefined"!=typeof Promise&&-1!==Promise.toString().indexOf("[native code]")?r.saveSvgAsPng(document.getElementById(e.svgid),"diagram.png"):console.error("This navigator does not support Promises")}).html("<svg width='15pt' height='15pt' viewBox='0 0 512 512' xmlns='http://www.w3.org/2000/svg'><g><polygon points='95 275 95 415 415 415 415 275 375 275 375 380 135 380 135 275'/><polygon points='220 30 220 250 150 175 150 245 250 345 350 245 350 175 280 250 280 30'/></g></svg>")}(this,e,t,n,i)}},{key:"removeContent",value:function(){var e=i.select("#"+this.containerid+" .girafe_container_std svg");e.size()>0&&i.select(".tooltip_"+e.attr("id")).remove();var t=i.select("#"+this.containerid+" div.girafe_container_std");t.size()>0&&t.remove();var n="."+this.tooltipClassname(),r=i.select(n);r.size()>0&&r.remove()}},{key:"addSvg",value:function(e,t){(this.removeContent(),i.select("#"+this.containerid).append("div").attr("class","girafe_container_std").html(e),i.select("body").append("div").attr("class",this.tooltipClassname()).style("position","absolute").style("opacity",0),t)&&function(e){var t=e.indexOf("{"),n=e.substring(t+1,e.lastIndexOf("}")),i=e.substring(0,t),r=i.substring(i.indexOf("(")+1,i.lastIndexOf(")")).split(",");function s(){return Function.apply(this,r)}return r.push(n),s.prototype=Function.prototype,new s}(t)()}},{key:"IEFixResize",value:function(e,t){if("IE 11"==s()||"MSIE"===s().substring(0,4)){var n=this.containerid,r=this.svgid;i.select("#"+r).classed("girafe_svg_ie",!0),i.select("#"+n+" div").classed("girafe_container_ie",!0).style("width",Math.round(100*e)+"%").style("padding-bottom",Math.round(e*t*100)+"%")}}},{key:"autoScale",value:function(e){var t=this.svgid;i.select("#"+t).style("width",e).style("height","100%").style("margin-left","auto").style("margin-right","auto")}},{key:"fixSize",value:function(e,t){var n=this.containerid,r=this.svgid;i.select("#"+r).attr("preserveAspectRatio","xMidYMin meet"),i.select("#"+n+" .girafe_container_std").style("width","100%"),i.select("#"+r).attr("width",e).attr("height",t),i.select("#"+r).style("width",e).style("height",t)}},{key:"setSizeLimits",value:function(e,t,n,r){var s=this.svgid;i.select("#"+s).style("max-width",e).style("max-height",n).style("min-width",t).style("min-height",r)}},{key:"removeContainerLimits",value:function(){if("IE 11"==s()||"MSIE"===s().substring(0,4));else{var e=this.containerid;i.select("#"+e).style("width",null).style("height",null)}}},{key:"animateToolbar",value:function(){var e=this.containerid;i.select("#"+e).on("mouseover",function(t){i.select("#"+e+" div.ggiraph-toolbar").transition().duration(200).style("opacity",.8)}).on("mouseout",function(t){i.select("#"+e+" div.ggiraph-toolbar").transition().duration(500).style("opacity",0)})}},{key:"animateGElements",value:function(e,t,n,r,s,o,a,l){var c=this.hoverClassname(),d=this.hoverKeyClassname(),u=this.hoverThemeClassname(),h=i.selectAll("#"+this.svgid+" *[title], #"+this.svgid+" *[data-id], #"+this.svgid+" *[key-id], #"+this.svgid+" *[theme-id]"),f="."+this.tooltipClassname(),g=this.svgid,p=this.containerid;h.on("mouseover",function(o){null!==this.getAttribute("theme-id")&&i.selectAll("#"+g+' *[theme-id="'+this.getAttribute("theme-id")+'"]').classed(u,!0);null!==this.getAttribute("key-id")&&i.selectAll("#"+g+' *[key-id="'+this.getAttribute("key-id")+'"]').classed(d,!0);null!==this.getAttribute("data-id")&&i.selectAll("#"+g+' *[data-id="'+this.getAttribute("data-id")+'"]').classed(c,!0);if(null!==this.getAttribute("title")){var h=i.select(f);if(h.transition().duration(s).style("opacity",e),a){var v=this.getAttribute("fill");h.style("background-color",v)}if(l){var y=this.getAttribute("stroke");h.style("border-color",y)}h.html(this.getAttribute("title")),h.html(h.text());var m=h.node().getBoundingClientRect(),w=i.select("#"+p).node().getBoundingClientRect();if(r){var S=i.event.pageX+t,b=S+m.width-(w.x+w.width);b>0&&(S-=b);var x=i.event.pageY+n,k=x+m.height-(w.y+w.height+window.pageYOffset);k>0&&(x-=k),h.style("left",S+"px").style("top",x+"px")}else h.style("left",t+w.left+"px").style("top",document.documentElement.scrollTop+w.y+n+"px")}}).on("mousemove",function(e){if(null!==this.getAttribute("title")){var s=i.select(f),o=s.node().getBoundingClientRect(),a=i.select("#"+p).node().getBoundingClientRect();if(r){var l=i.event.pageX+t,c=l+o.width-(a.x+a.width);c>0&&(l-=c);var d=i.event.pageY+n,u=d+o.height-(a.y+a.height+window.pageYOffset);u>0&&(d-=u),s.style("left",l+"px").style("top",d+"px")}else s.style("left",t+a.left+"px").style("top",document.documentElement.scrollTop+a.y+n+"px")}}).on("mouseout",function(e){null!==this.getAttribute("theme-id")&&i.selectAll("#"+g+' *[theme-id="'+this.getAttribute("theme-id")+'"]').classed(u,!1);null!==this.getAttribute("key-id")&&i.selectAll("#"+g+' *[key-id="'+this.getAttribute("key-id")+'"]').classed(d,!1);null!==this.getAttribute("data-id")&&i.selectAll("#"+g+' *[data-id="'+this.getAttribute("data-id")+'"]').classed(c,!1);null!==this.getAttribute("title")&&i.select(f).transition().duration(o).style("opacity",0)})}},{key:"setSelected",value:function(e){this.dataSelected=e,this.refreshSelected(),this.inputId&&Shiny.onInputChange(this.inputId,this.dataSelected)}},{key:"setKeySelected",value:function(e){this.dataKeySelected=e,this.refreshKeySelected(),this.inputKeyId&&Shiny.onInputChange(this.inputKeyId,this.dataKeySelected)}},{key:"setThemeSelected",value:function(e){this.dataThemeSelected=e,this.refreshThemeSelected(),this.inputThemeId&&Shiny.onInputChange(this.inputThemeId,this.dataThemeSelected)}},{key:"selectizeMultiple",value:function(){var e=this;i.selectAll("#"+this.svgid+" *[data-id]").on("click",function(t,n){var r=e.dataSelected,s=i.select(this).attr("data-id"),o=r.indexOf(s);o<0?r.push(s):r.splice(o,1),e.dataSelected=r,e.refreshSelected(),e.inputId&&Shiny.onInputChange(e.inputId,e.dataSelected)})}},{key:"selectizeSingle",value:function(){var e=this;i.selectAll("#"+this.svgid+" *[data-id]").on("click",function(t,n){var r=e.dataSelected,s=i.select(this).attr("data-id");r=r.indexOf(s)<0?[s]:[],e.dataSelected=r,e.refreshSelected(),e.inputId&&Shiny.onInputChange(e.inputId,e.dataSelected)})}},{key:"selectizeNone",value:function(){i.selectAll("#"+this.svgid+" *[data-id]").on("click",null)}},{key:"selectizeKeyMultiple",value:function(){var e=this;i.selectAll("#"+this.svgid+" *[key-id]").on("click",function(t,n){var r=e.dataKeySelected,s=i.select(this).attr("key-id"),o=r.indexOf(s);o<0?r.push(s):r.splice(o,1),e.dataKeySelected=r,e.refreshKeySelected(),e.inputKeyId&&Shiny.onInputChange(e.inputKeyId,e.dataKeySelected)})}},{key:"selectizeKeySingle",value:function(){var e=this;i.selectAll("#"+this.svgid+" *[key-id]").on("click",function(t,n){var r=e.dataKeySelected,s=i.select(this).attr("key-id");r=r.indexOf(s)<0?[s]:[],e.dataKeySelected=r,e.refreshKeySelected(),e.inputKeyId&&Shiny.onInputChange(e.inputKeyId,e.dataKeySelected)})}},{key:"selectizeKeyNone",value:function(){i.selectAll("#"+this.svgid+" *[key-id]").on("click",null)}},{key:"selectizeThemeMultiple",value:function(){var e=this;i.selectAll("#"+this.svgid+" *[theme-id]").on("click",function(t,n){var r=e.dataThemeSelected,s=i.select(this).attr("theme-id"),o=r.indexOf(s);o<0?r.push(s):r.splice(o,1),e.dataThemeSelected=r,e.refreshThemeSelected(),e.inputThemeId&&Shiny.onInputChange(e.inputThemeId,e.dataThemeSelected)})}},{key:"selectizeThemeSingle",value:function(){var e=this;i.selectAll("#"+this.svgid+" *[theme-id]").on("click",function(t,n){var r=e.dataThemeSelected,s=i.select(this).attr("theme-id");r=r.indexOf(s)<0?[s]:[],e.dataThemeSelected=r,e.refreshThemeSelected(),e.inputThemeId&&Shiny.onInputChange(e.inputThemeId,e.dataThemeSelected)})}},{key:"selectizeThemeNone",value:function(){i.selectAll("#"+this.svgid+" *[theme-id]").on("click",null)}},{key:"refreshSelected",value:function(){var e=this.selectedDataClassname(),t=this.svgid,n=i.select("#"+t);n.selectAll("*[data-id]").classed(e,!1);var r=this;i.selectAll(r.dataSelected).each(function(t,i){n.selectAll('*[data-id="'+r.dataSelected[i]+'"]').classed(e,!0)})}},{key:"refreshKeySelected",value:function(){var e=this.selectedKeyClassname(),t=this.svgid,n=i.select("#"+t);n.selectAll("*[key-id]").classed(e,!1);var r=this;i.selectAll(r.dataKeySelected).each(function(t,i){n.selectAll('*[key-id="'+r.dataKeySelected[i]+'"]').classed(e,!0)})}},{key:"refreshThemeSelected",value:function(){var e=this.selectedThemeClassname(),t=this.svgid,n=i.select("#"+t);n.selectAll("*[theme-id]").classed(e,!1);var r=this;i.selectAll(r.dataThemeSelected).each(function(t,i){n.selectAll('*[theme-id="'+r.dataThemeSelected[i]+'"]').classed(e,!0)})}},{key:"zoomOn",value:function(){var e=this.svgid;i.select("#"+this.containerid).call(this.zoomer.on("zoom",function(){i.select("#"+e+" g").attr("transform",i.event.transform)}))}},{key:"zoomIdentity",value:function(){i.select("#"+this.containerid).call(this.zoomer.transform,i.zoomIdentity)}},{key:"zoomOff",value:function(){i.select("#"+this.containerid).call(this.zoomer.on("zoom",null))}},{key:"isSelectable",value:function(){var e=this.svgid,t=i.select("#"+e);return t.selectAll("*[data-id]").size()>0||t.selectAll("*[key-id]").size()>0||t.selectAll("*[theme-id]").size()>0}},{key:"lasso_on",value:function(e){var t=this.svgid,n=this,r=i.lasso();r=r.closePathSelect(!0).closePathDistance(100).items(i.select("#"+t).selectAll("*[data-id]")).targetArea(i.select("#"+t)).on("start",function(){}).on("draw",function(){}).on("end",function(){r.selectedItems().each(function(t,r){i.select(this).classed(n.selectedDataClassname(),!0);var s=i.select(this).attr("data-id"),o=n.dataSelected.indexOf(s);o<0&&e?n.dataSelected.push(s):o>=0&&!e&&n.dataSelected.splice(o,1)}),n.refreshSelected(),i.select("#"+t).on(".dragstart",null).on(".drag",null).on(".dragend",null),n.inputId&&Shiny.onInputChange(n.inputId,n.dataSelected)}),i.select("#"+t).call(r)}}]),e}();function l(e){return new a(e)}n.d(t,"newgi",function(){return l})}]);</script>
<script>function isArray(x) {
    return x.constructor.toString().indexOf("Array") > -1;
}

function set_reactive(x, id ){

  Shiny.addCustomMessageHandler(id + '_set', function(message) {
    if( typeof message === 'string' ) {
      x.setSelected([message]);
    } else if( isArray(message) ){
      x.setSelected(message);
    }
  });
  Shiny.addCustomMessageHandler(id + '_key_set', function(message) {
    if( typeof message === 'string' ) {
      x.setKeySelected([message]);
    } else if( isArray(message) ){
      x.setKeySelected(message);
    }
  });
  Shiny.addCustomMessageHandler(id + '_theme_set', function(message) {
    if( typeof message === 'string' ) {
      x.setThemeSelected([message]);
    } else if( isArray(message) ){
      x.setThemeSelected(message);
    }
  });

}



HTMLWidgets.widget({

  name: "girafe",

  type: "output",

  factory: function(el, width, height) {
    var ggobj = ggiraphjs.newgi(el.id);

    return {
      renderValue: function(x) {
        ggobj.setSvgId(x.uid);
        ggobj.addStyle(x.settings.tooltip.css,
            x.settings.hover.css, x.settings.hoverkey.css, x.settings.hovertheme.css,
            x.settings.capture.css, x.settings.capturekey.css, x.settings.capturetheme.css);
        ggobj.setZoomer(x.settings.zoom.min, x.settings.zoom.max);
        ggobj.addSvg(x.html, x.js);
        ggobj.animateGElements(x.settings.tooltip.opacity,
            x.settings.tooltip.offx, x.settings.tooltip.offy,
            x.settings.tooltip.use_cursor_pos,
            x.settings.tooltip.delay.over, x.settings.tooltip.delay.out,
            x.settings.tooltip.usefill, x.settings.tooltip.usestroke);
        ggobj.animateToolbar();

        if( !x.settings.sizing.rescale ){
          var width_ = d3.select(el).style("width");
          var height_ = d3.select(el).style("height");
          ggobj.fixSize(width_, height_);
        } else if( HTMLWidgets.shinyMode ){
          ggobj.autoScale("100%");
          ggobj.IEFixResize(1, 1/x.ratio);
          var maxWidth = width;
          var maxHeight = height;
          try {
            const box = d3.select("#" + ggobj.svgid).property("viewBox").baseVal;
            maxWidth = box.width;
            maxHeight = box.height;
          } catch (e) {}
          ggobj.setSizeLimits(maxWidth+'px', 0, maxHeight+'px', 0);
          ggobj.removeContainerLimits();
        } else {
          ggobj.autoScale(Math.round(x.settings.sizing.width * 100) + "%");
          ggobj.IEFixResize(x.settings.sizing.width, 1/x.ratio);
          ggobj.setSizeLimits("unset", "unset", "unset", "unset");
          ggobj.removeContainerLimits();
        }

        var addSelection = ggobj.isSelectable() &&
          (( HTMLWidgets.shinyMode &&
            ( x.settings.capture.only_shiny ||
              x.settings.capturekey.only_shiny ||
              x.settings.capturetheme.only_shiny)
          ) ||
          ( !HTMLWidgets.shinyMode &&
            ( !x.settings.capture.only_shiny ||
              !x.settings.capturekey.only_shiny ||
              !x.settings.capturetheme.only_shiny)
          ));

        var addZoom = true;
        if( x.settings.zoom.min === 1 && x.settings.zoom.max <= 1 ){
          addZoom = false;
        }

        if( addSelection && x.settings.capturetheme.type == "single" ){
          ggobj.selectizeThemeSingle();
          if( typeof x.settings.capturetheme.selected === 'string' ) {
            ggobj.setThemeSelected([x.settings.capturetheme.selected]);
          }
        } else if( addSelection && x.settings.capturetheme.type == "multiple" ){
          ggobj.selectizeThemeMultiple();
          if( typeof x.settings.capturetheme.selected === 'string' ) {
            ggobj.setThemeSelected([x.settings.capturetheme.selected]);
          } else if( isArray(x.settings.capturetheme.selected) ){
            ggobj.setThemeSelected(x.settings.capturetheme.selected);
          }
        } else {
          ggobj.selectizeThemeNone();
        }

        if( addSelection && x.settings.capturekey.type == "single" ){
          ggobj.selectizeKeySingle();
          if( typeof x.settings.capturekey.selected === 'string' ) {
            ggobj.setKeySelected([x.settings.capturekey.selected]);
          }
        } else if( addSelection && x.settings.capturekey.type == "multiple" ){
          ggobj.selectizeKeyMultiple();
          if( typeof x.settings.capturekey.selected === 'string' ) {
            ggobj.setKeySelected([x.settings.capturekey.selected]);
          } else if( isArray(x.settings.capturekey.selected) ){
            ggobj.setKeySelected(x.settings.capturekey.selected);
          }
        } else {
          ggobj.selectizeKeyNone();
        }

        if( addSelection && x.settings.capture.type == "single" ){
          ggobj.selectizeSingle();
          addSelection = false;
          if( typeof x.settings.capture.selected === 'string' ) {
            ggobj.setSelected([x.settings.capture.selected]);
          }

        } else if( addSelection && x.settings.capture.type == "multiple" ){
          ggobj.selectizeMultiple();
          if( typeof x.settings.capture.selected === 'string' ) {
            ggobj.setSelected([x.settings.capture.selected]);
          } else if( isArray(x.settings.capture.selected) ){
            ggobj.setSelected(x.settings.capture.selected);
          }
        } else {
          ggobj.selectizeNone();
          addSelection = false;
        }

        ggobj.addUI(addSelection, addZoom,
          x.settings.toolbar.saveaspng,
          'ggiraph-toolbar-' + x.settings.toolbar.position);

        if( HTMLWidgets.shinyMode ){
          ggobj.setInputId(el.id + "_selected");
          ggobj.setInputKeyId(el.id + "_key_selected");
          ggobj.setInputThemeId(el.id + "_theme_selected");
          set_reactive(ggobj, el.id );
        }

      },

      resize: function(width, height) {
        //ggobj.setSize(width, height);
      }

    };
  }
});
</script>

Tal vez porque soy un poco paranoico o porque trabajo en una empresa que va codo a codo con algunos gigantes de la industria pero el caso es que decidí ir a Facebook, Twitter, Google y Spotify a pedir los datos que tienen de mí.

En este post vamos a jugar un poco con **ggplot** y con los datos de reproducciones que tuve en el último año. Empezaremos viendo en dónde pueden pedir la información que tienen de ustedes y después haremos unas tres gráficas con algunas #BibliotecasNoLibrerias que me aventuré a conocer.

Recuerden que estos posts son para principiantes hechos por otro principiante. Si consideran que algo  del código se pudo hacer mejor, denle _fork_ al repo y manden su _pull request_ para que todos podamos aprender algo nuevo.

Dicho esto, a darle que es mole de olla.

## Consiguiendo los datos

Lo primero es ir a la página  [https://www.spotify.com/mx/account/privacy/](https://www.spotify.com/mx/account/privacy/). Después de  autenticarse con su usuario y contraseña les va a aparecer esto:

![](https://github.com/nerudista/DataViz/blob/master/MySpotify/MySpotify/imagenes/privacidad1.png?raw=true)

Si van bajando sobre esa página van a encontrar la sección **"Descargar tus datos"**:

![](https://github.com/nerudista/DataViz/blob/master/MySpotify/MySpotify/imagenes/privacidad2.png?raw=true)

Si leen como atención, cosa que yo no hice, se darán cuenta que les van a dar el historial de streaming del último año y sus datos de facturación así como sus playlist entre otras cosas.

Sigan el proceso que viene descrito en un unos pocos días, en mi caso fueron 3, les llegará un enlace para que puedan descargar su información. El .zip que me enviaron contenía los siguientes archivos:

![datos recibidos](https://github.com/nerudista/DataViz/blob/master/MySpotify/MySpotify/imagenes/data_recibida.png?raw=true)

Por favor lean el archivo "Read Me First.pdf". Dentro viene un enlace donde encontraran el diccionario de datos de cada archivo recibido. Para este ejercicio sólo vamos a trabajar con los archivos encerrados en el recuadro rojo. Esos archivos son mi historial de reproducción del último año.

El contenido lo pueden ver en esta imagen:

![streaming](https://github.com/nerudista/DataViz/blob/master/MySpotify/MySpotify/imagenes/streaming.png?raw=true)

## Haciendo bocetos

Del ejercicio de los [Pats](https://tacosdedatos.com/primeros-pasos-con-ggplot-y-altair)  aprendí que lo mejor era primero hacer bocetos para después tirarme un clavado al código.

Este es el primer boceto que hice:

![boceto](https://github.com/nerudista/DataViz/blob/master/MySpotify/MySpotify/imagenes/boceto.png?raw=true)

El resto del documento lo pueden ver el repo dentro del archivo [**MySpotifyIdeas.pdf**](https://github.com/nerudista/DataViz/blob/master/MySpotify/MySpotify/MySpotifyIdeas.pdf)


## Creando mi _theme_

Crear un theme ayuda a simplificar el código que tenemos que escribir para cada gráfica. Para este caso conseguí los códigos de colores que usa Spotify en su aplicación y el font más parecido al que usaban en su versión anterior. El de la nueva ya tenía costo así que por economía nos veremos _oldies but goodies_

Este es el código para el theme que utilizaremos:

```r
#theme
#colors
# 1db954, 1ed760, ffffff, 191414

theme_spoty <- theme(
    #Esto pone negro el titulo , los ejes, etc
    plot.background = element_rect(fill = '#191414', colour = '#191414'), 
    #Esto pone negro el panel de la gráfica, es decir, sobre lo que van los bubbles
    panel.background = element_rect(fill="#191414",color="#191414"),
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    text = element_text(color = "#1db954",
                        family="Gotham"   #esta es la fuente que instalé en mi lap
    ),
    # limpiar la gráfica
    axis.line = element_line(colour="#191414"),
    axis.title=element_blank(),
    axis.text=element_blank(),
    axis.ticks=element_blank(),
    # ajustar titulos y notas
    plot.title = element_text(size=32,
                              color="#1db954"),
    plot.subtitle = element_text(size=20,
                              color="#1db954"),
    plot.caption = element_text(
      color="#1db954",
      face="italic",
      size=13
    ),
    complete=FALSE
  )

```

Esta vez aprendí la diferencia entre el `plot.background` y `panel.background`. El panel es el espacio donde se pintan las figuras y el plot abarca el espacio del título, los ejes y el caption.

Fuera de eso, lo demás es limpieza de ejes y asignar fuentes y colores. Anímense a cambiar los valores para que vean como cambia su gráfica.

## Importando los datos

Como recibí tres archivos con los datos de streaming tenemos que importarlos y después unirlos. Eso se hace bien fácil con:

```r
#importar json como dataframe
df0 <- fromJSON("./Data/StreamingHistory0.json")
df1 <- fromJSON("./Data/StreamingHistory1.json")
df2 <- fromJSON("./Data/StreamingHistory2.json")

#Unir los dataframes
data <- rbind(df0,df1,df2)

#Cargar la fuente desde mi Windows 
windowsFonts(`Gotham` = windowsFont("Gotham"))
```
Al final cargo la fuente que voy a utilizar para las gráficas. Como tengo Windows sólo descargué la fuente de un sitio de internet y luego hice _drag and drop_ a mi pantalla de Font Settings. Una vez instalada la fuente en Windows ya puedo usarla en R con ese comando.

## Historical Bubble Chart

No sé si se llame así pero me gusta el nombre. Lo que vamos a hacer es graficar verticalmente bubbles que tendran como _encoding_ el mes de reproducción en la posición y el tamaño para los minutos de reproducción. Es decir, algo así:

![bubbles](https://github.com/nerudista/DataViz/blob/master/MySpotify/MySpotify/graficas/TopMensual.png?raw=true)

Antes de ver el código intentemos desmenuzar la gráfica para saber qué es lo que tenemos que hacer.

Cada circulo representa el artista que más escuché en el mes. Esto significa que tengo que agrupar el tiempo reproducido de cada canción por artista y por mes y luego sacar el top 1 de cada mes. Algo así:

```r
dfArtistaMes <- data %>% 
  group_by(mesAño,artistName)%>% 
  summarise(minutosMes= round((sum(minPlayed)))) %>% 
  top_n(1)  #me da el top1 de cada grupo creado arriba
```

Intentemos pensar en las capas que necesitamos para crear la gráfica:

1. Una capa para las burbujas. Estan van ordenadas en el mismo punto X pero ordenadas cronológicamente, de arriba hacia abajo, en el eje Y.
2. Una capa para los textos de la derecha: mes-año.
3. Una capa para los textos de la derecha: artista más escuchado por mes.
4. Una capa para los textos de la derecha: minutos.
5. Una capa para la línea blanca que atraviesa las bolitas para dar idea de continuidad (_conque aplicando Teoría Gestalt eh?_).

El código para crear estas capas fue:

```r
plotArtistaMes <-ggplot(data=dfArtistaMes,
       aes( x=1, 
            y=reorder(mesAño, desc(mesAño))  #ordenar ascendente el mesAño 
       )
       )+
  geom_point( aes(                    #Capa 1
                  size=minutosMes, 
                  color=artistName),
              show.legend = FALSE,
              alpha = 1
              )  +
  geom_text(aes(x=1.06,label=mesAño), #Capa 2
            color="#FFFFFF",
            family="Gotham",
            size=6,
            hjust="left")+
  geom_text(aes(x=.94,                #Capa 3
                label=artistName,
                color=artistName),
            family="Gotham",
            size=6,
            show.legend = FALSE,
            hjust="right")+
  geom_text(aes(x=.94, label=paste(minutosMes,"m")),   #Capa 4
            hjust="right",
            size=6,
            color="#FFFFFF",
            family="Gotham",
            vjust=1.8)+
  geom_line(data=data.frame(x=c(1,1),y=c(1,13)),     #Capa 5
            aes(x=x, y=y),
            alpha=0.4,
            size=.85,
            color="#FFFFFF",
            linetype = "dotted")+
  scale_x_discrete(limits=c(1))+
  scale_radius(range = c(4, 26))+
  scale_color_brewer(palette="Dark2")+
  labs(title="Mi Spotify en el Último Año",
       subtitle="Minutos escuchados por artista",
       caption="Hecho por @nerudista con datos de Spotify") +
  theme(
    plot.title = element_text(hjust = 0.5),
    plot.subtitle = element_text(hjust = 0.5)
  )+
  theme_spoty  ; plotArtistaMes
```

Para esta gráfica no busqué en internet cómo hacerla sino que me imaginé cómo armarla desde cero. Si usteden encuentran una mejor opción, ¡compartanla!.

Lo que hice fue poner todas las burbujas en el valor `x=1` y para las capas de los textos le resté, o sumé, algunas centésimas para ponerlas a la derecha o la izquierda. Después ya es cosa de poner título, subtítulo  y caption. Centrar los primeros dos y aplicar el _theme_.

Bueno, ya que estamos con burbujas intentemos hacer algo más coqueto.

## _Circle Packing_ para mostrar cada canción escuchada

Desde hacía buen rato había querido hacer un treemap pero en burbujas. _Circle Packing_ le llaman.

Esta es la gráfica que haremos:

![circlePacking](https://github.com/nerudista/DataViz/blob/master/MySpotify/MySpotify/graficas/SingleBubble.png?raw=true)

Lo que intento visualizar es qué canciones escuché por más tiempo durante el último año. Para esto voy a poner el tamaño de la burbuja como _encoding_ y el color será diferente por cada canción. 

Esta vez tenemos que agrupar no por artista ni por mes sino por canción en todo el año:

```r
dfCanciones <- data %>%
  group_by(artistName,trackName) %>%
  summarise(min=sum(minPlayed))%>%
  filter (min > 5)

```
Vamos a necesitar una nueva #BibliotecasNoLibreria para crear esta visualización:


```r
#install.packages("packcircles")
library("packcircles")


```
Ya con el data frame agrupado por cancion, vamos a meter la canción (_trackName_) a la función `circleProgressiveLayout` para que nos cree un nuevo dataframe con la posición x,y y el radio de cada burbuja:


x | y | radio
---------|----------|---------
 -1.34224959 | 0.00000000 | 1.342250
 1.95766469 | 0.00000000 | 1.957665
 ... | ... |...
 5.05310636 | 4.81689588 | 2.509851

Después hay que meter ese nuevo data frame a la función `circleLayoutVertices`:

```r
# los npoints dicen las caras de la figura 3-triangulo, 4-cuadrado
# con el 50 ya se parece a un círculo
dat.gg <- circleLayoutVertices(packing, npoints=50)
```

Lo que queda es ya crear la gráfica:

```r
plotSingleBubble <- ggplot()+
  # Make the bubbles
  geom_polygon(data=dat.gg, aes(x,
                                y,
                                group=id,
                                colour = "black",
                                alpha = 0.6,
                                fill=as.factor(id)
                                )
               ) +
  scale_size_continuous(range = c(1,5)) +
  labs(title="Un Universo de Música",
       subtitle="Minutos de reproducción por canción ",
       caption="Hecho por @nerudista con datos de Spotify") +
  scale_color_brewer(palette="Dark2")+
  #theme_void()+
  theme_spoty+
  theme(legend.position="none") +
  coord_equal(); plotSingleBubble

```

Poquito código y queda algo bonito. Sin embargo no me dice qué canción escuché más ni cuántos minutos escuché cada uno. No pintamos texto sobre las burbujas porque o no cabe o es tanto texto que tapa las figuras.

¿Y si cuando pase el mouse me dijera qué canción es y cuánto la escuché? Eso ya sería hacer algo interactivo. Y pues ...

## _Circle Packing_ interactivo

Primero vamos a importar la biblioteca para la parte interactiva. Luego toca crear una columna nueva en el data frame con el texto que queremos desplegar:

```r

#install.packages("ggiraph")
library(ggiraph)

# Add a column with the text you want to display for each bubble:
dfSimpleBubble$text <- paste("Canción: ",dfSimpleBubble$dfCanciones.trackName, "\n", "Min:", dfSimpleBubble$dfCanciones.min)
```
Lo que sigue es crear de nuevo la gráfica de burbujas pero agregando el tooltip con la línea:
`tooltip= dfSimpleBubble$text[id]`:

```r
plotSingleBubbleInter <- ggplot()+
  geom_polygon_interactive(data=dat.gg,
                           aes(x,y,
                               group=id,
                               fill=as.factor(id),
                               tooltip= dfSimpleBubble$text[id],
                               family="Gotham",
                               data_id = id),
                           colour = "black", alpha = 0.6)+
  theme_spoty +
  scale_color_brewer(palette="Dark2")+
  theme(legend.position="none") +
  labs(title="Un Universo de Música",
       subtitle="Minutos de reproducción por canción ",
       caption="Hecho por @nerudista con datos de Spotify") +
  coord_equal()

widg <- ggiraph(ggobj = plotSingleBubbleInter, width_svg = 6, height_svg = 9)

```

Ya con la gráfica construido hay que pasarla por `ggiraph` para que cree la parte interactiva. El resultado de esto es un archivo html con su respectiva carpeta que contiene los archivos necesarios para crear la visualización web. Si revisan el código van a darse cuenta que usa javascript por debajo para construir la visualización. ¡Un pasito más cerca de D3!

Vean nomás qué chulada queda:

![interactive_bubble](https://github.com/nerudista/DataViz/blob/master/MySpotify/MySpotify/imagenes/interactive_bubble.png?raw=true)
<div id="htmlwidget_container">
    <div id="htmlwidget-8bb1dcfd60108c6f90b8" class="girafe html-widget" style="width:960px;height:500px;">
    </div>
</div>

De mis pendientes quedan poder poner color por artista y no por canción. Tengo que agarrarle más la onda a las funciones de los vértices para poder tener en el data frame alguna variable por artista que me deje hacer el fill por ese nuevo campo y no por `id`.

## Lollipops 

Y ya para acabar con los `geom_points()` haremos una grafica de ¿paleta?. No sé cómo le digan en español pero en inglés les dicen _lollipop_.

Empecemos viendo el resultado final para desmenuzarla de nuevo:

![top5](https://github.com/nerudista/DataViz/blob/master/MySpotify/MySpotify/graficas/plotTop5.png?raw=true)

La gráfica muestra los 5 artistas más escuchados e inmediatamente después muestra qué canción, de ese artista, fue la más escuchada. Ojo aquí, no es el top 5 de artistas y el top 5 de canciones. Es el top 5 de artistas con su respectiva canción más reproducida. Este pequeño detalle me hizo darle varias vueltas a la solución. 

Lo primero es crear los data frames para tener los artistas y las canciones:

```r
#Crear data frame con el tiempo total por cada canción junto con el artista.
#Necesito el artista para filtrar más adelante.
dfCanciones <- data %>%
  group_by(artistName,trackName) %>%
  summarise(min=sum(minPlayed))%>%
  filter (min > 5)


# Crear df el top n de artistas por minutos reproducidos en el año
dfArtistaAnio <- data%>%
  group_by(artistName)%>%
  summarise(min=sum(minPlayed)) %>%
  top_n(5)%>%   # de una vez me quedo con el top 5
  arrange(min)  # ordeno ya encarrerado el ratón

```

Ya con ambos dataframes voy a crear uno nuevo, basado en `dfCanciones`, donde esté agrupado por `artistName`.  No le voy a poner que sumarise nada sólo quiero la estructura del `group by` para aplicarla en el siguiente paso. 

Sigue crear el df `dfTopCanciones` que es el resultado de tomar el top 1 del df `artistas` para finalmente hacerle merge al df `dfArtistaAnio`. Al final nos quedan los 5 artistas más escuchados con su respectiva canción.

Sé que suena enredado pero solo son 3 comandos. Muévanle a esos tres para que vean que resultados diferentes les dan para que tenga cierta lógica:

```r
# crear un df con el group by solo por artistName 
artistas <- dfCanciones %>% group_by(artistName)

# filtrar el df artistas tomando el registro con más minutos. Va inverso porque no hay max_rank()
# sino min_rank(). Solo tomo un row: el más alto
dfTopCanciones <- filter(artistas, min_rank(desc(min)) <= 1 )

# hago un join entre el df dfArtistaAnio y dfTopCanciones por artistName
# así solo me quedan los top artistas con su canción más escuchada
dfCancionesArtistasTop <- merge(x=dfArtistaAnio, y=dfTopCanciones, by="artistName", all.x = TRUE)
```

La salida del último df es:

artistName | min.X | trackName | min.Y
---------|----------|---------|---------
 Arcade Fire | 1993.59 | The Suburbs | 224.74
 Gallina Pintadita | 1781.42 | Hormiguita | 153.22

Hasta ahí parece que vamos bien pero ¿cómo usamos esta salida para graficar? Podría usar dos capas donde en el primer `aes(x= ?)` use `artistName` y en la otra el `trackName`. Sin embargo, cuando junte las capas el orden se me rompe.

Como todo en esta vida hay más de una forma de solucionar las cosas y la que se me ocurrió fue generar esta salida:

item | min | type |
---------|----------|---------|
 Arcade Fire | 1993.59 | A |
 Gallina Pintadita | 1781.42 | A |
 ... | ... | ... |
 The Suburbs | 224.74 | C

 Donde el `ìtem` es la canción o el artista, `min` los minutos reproducidos y en `type` "C" si es canción y "A" si es artista. Esta variable es importante para poder hacer formato condicional dentro de la misma capa. Con este df ya no necesito hacer dos capas sino una sola condicionando la salida por el tipo de item.

 Este es el código para crear la salida que buscamos:

```r
 dfTemp1 <- data.frame( item = dfCancionesArtistasTop$artistName,
                                          min = dfCancionesArtistasTop$min.x,
                                          type = "A")%>% arrange(-min)

# manualmente pongo el orden en que quiero que aparezcan los artistas
dfTemp1$order <- c(1,3,5,7,9)


dfTemp2 <- data.frame( item = dfCancionesArtistasTop$trackName,
                       min = dfCancionesArtistasTop$min.y,
                       type = "C")

# manualmente pongo el orden en que quiero que aparezcan las canciones
dfTemp2$order <- c(2,6,8,4,10)

dfCancionesArtistasTopList=rbind(dfTemp1,dfTemp2)
```

Noten que también creamos una columna con el orden en específico en que queremos que aparezcan los items. Como eran pocos registros lo hice manual. Si fueran mcuhos más, toca pensar como hacerlo en automático.

Intentemos dibujar la gráfica desde la gramática de gráficos:

1. Una capa para las líneas del _lollipop_.
2. Una capa para el círculo de la paleta.
3. Una capa para los textos (minutos escuchados).
4. LA capa de título, subtítulo, etc.

Con esto en mente es más fácil saber qué construir:

```r
#ahora a pintar la gráfica
plotTop5 <- ggplot(data=dfCancionesArtistasTopList,
       aes(x=item,
           y=min
       ))+
  geom_segment(data=dfCancionesArtistasTopList ,
               aes(x=reorder(item,-order),
                   xend=item,
                   y=0,
                   yend=min
                   ),
               color=ifelse(dfCancionesArtistasTopList$type=="A","#67A61F","#E72A8A"),
               size=2
               )+
  geom_point(
             color=ifelse(dfCancionesArtistasTopList$type=="A","#67A61F","#E72A8A"),
             size=7
  )+
  geom_text(aes(label=paste(round(min,0)," min")),
            color=ifelse(dfCancionesArtistasTopList$type=="A","#67A61F","#E72A8A"),
            size=4,
            #color="#FFFFFF",
            family="Gotham",
            nudge_x = -.3)+
  coord_flip()+
  scale_color_brewer(palette="Dark2")+
  ylab("")+
  xlab("")+
  theme(legend.position="none") +
  labs(title="Top 5 de Artistas",
       subtitle="Con su canción más escuchada",
       caption="Hecho por @nerudista con datos de Spotify") +
  theme(
    text = element_text(color = "#1db954",
                        family="Gotham"
    ),
    #Esto pone negro el titulo , los ejes, etc
    plot.background = element_rect(fill = '#191414', colour = '#191414'), 
    #Esto pone negro el panel de la gráfica, es decir, sobre lo que van los lollipops
    panel.background = element_rect(fill="#191414",color="#191414"),
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    plot.title = element_text(size=32,
                              color="#1db954"),
    plot.subtitle = element_text(size=20,
                                 color="#E72A8A"),
    plot.caption = element_text(
      color="#1db954",
      face="italic",
      size=13
    ),
    axis.text=element_text(family = "Gotham",
                           size=13,
                           color="#FFFFFF"),
    axis.ticks=element_blank(),
    axis.text.x = element_blank(),
  );plotTop5

```
Noten dos cosas:

1. El uso el `ifelse` con la variable `type` que creamos. Podemos jugar con el color dependiendo de si es canción o artista.
2. No apliqué mi _theme_ porque en él desaparezco las leyendas de los ejes y aquí los necesito para mostrar el nombre de la canción y el artista sobre el eje Y. Además, quería mostrarles la diferencia entre un `+theme_spoty` y tooooodo lo que tuve que poner para arreglar la gráfica. ¿Ven porqué están chidos los _themes_?

## Comentarios finales

Después del ejercicio de los [Pats](https://tacosdedatos.com/primeros-pasos-con-ggplot-y-altair) intenté hacer cosas nuevas usando bibliotecas nuevas. No le tengan miedo. Anímense. Poco a poco irán saliendo cosas mejores. La cosa es no dejar de aprender.

Por otro lado, los aliento a ir pos sus datos a todas las plataformas en las que estén inscritos. Son sus datos y tienen derecho a saber qué conocen de ustedes. En Spotify no encontré nada especial pero en otros servicios sí me he ido para atrás con lo que han colectado de mí. Cultivemos una cultura de la responsabilidad de los datos. Son parte de nustra vida.

Por último, les recuerdo que este año Github está duplicando las donaciones que le hagan a [@tacosdedatos](https://github.com/sponsors/chekos). Con esa lana se puede pagar un chorro de cosas para mantener el sitio arriba y hasta para rifar uno que otro libro, si se junta la lana. Un dolar al mes es menos que una cerveza al día. ¿Ah, verdad?

## Recursos

Para esta entrada usé muchísimo la página [Data to Viz](https://www.data-to-viz.com/). Ahí van a encontrar chorro de ejemplos en R, Python y hasta en otras herramientas. 

El repo con el código está en:

[https://github.com/nerudista/DataViz/tree/master/MySpotify](https://github.com/nerudista/DataViz/tree/master/MySpotify)

Enren a verlo poque hay código que no puse en el blog y que en necesario para que las gráficas funcionen. Ya saben, cosa de economía para que el blog no se haga eterno y lo boten a media lectura.

***

¿Qué te pareció la nota? [Mandanos un tuit a @tacosdedatos](https://twitter.com/share?text=Obvio+que+estuvo+super+el+blog+%40tacosdedatos+%F0%9F%8C%AE) o [a @nerudista](https://twitter.com/share?text=Obvio+que+estuvo+super+el+blog+%40tacosdedatos+y+%40nerudista+%F0%9F%8C%AE) o envianos un correo a [✉️ sugerencias@tacosdedatos.com](mailto:sugerencias@tacosdedatos.com?subject=Sugerencia&body=Hola-holaaa). Y recuerda que puedes subscribirte a nuestro boletín semanal aquí debajo.


<script type="application/htmlwidget-sizing" data-for="htmlwidget-8bb1dcfd60108c6f90b8">{"viewer":{"width":450,"height":350,"padding":15,"fill":true},"browser":{"width":960,"height":500,"padding":40,"fill":false}}</script>
