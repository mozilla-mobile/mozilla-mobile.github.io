---
title: Introducing ScrollingCardView for iOS
tags: mozilla,iOS,Prox,Swift,widget,library
---
For [Project Prox][Prox], we were asked to implement a
design<sup><a href="#notes">1</a></sup> like this:

<div align="center">
  <img src="/im/posts/scroll-card-view.gif" alt="Scrolling the card view">
</div>

Specifically, this is a card view that:

* Hugs its content, dynamically expanding the height when the content does
* Will scroll its content if the content is taller than the card

After some searching, we discovered that no such widgets existed! Hoping our
efforts could be useful to others, we created [the ScrollingCardView
library][GitHub].

## Usage
ScrollingCardView is used much like any other view.

First, create your view, enable autolayout, and add it to the view hierarchy:
```swift

    let cardView = ScrollingCardView()
    cardView.translatesAutoresizingMaskIntoConstraints = false
    parentView.addSubview(cardView) // e.g. parent could be the ViewController's

```

Then constrain the card view as you would any other view:
```swift

    NSLayoutConstraint.activate([
        cardView.topAnchor.constraint(
            equalTo: topLayoutGuide.bottomAnchor, constant: 16),
        cardView.leadingAnchor.constraint(
            equalTo: view.leadingAnchor, constant: 16),
        cardView.trailingAnchor.constraint(
            equalTo: view.trailingAnchor, constant: -16),

        // If you don't constrain the height, the card
        // will grow to match its intrinsic content size.

        // Or use lessThanOrEqualTo to allow your card
        // view to grow only until a certain size, e.g.
        // the size of the screen.
        cardView.bottomAnchor.constraint(
            lessThanOrEqualTo: bottomLayoutGuide.topAnchor, constant: -16),

        // Or you can constrain it to a particular height:
        // cardView.bottomAnchor.constraint(
        //     equalTo: bottomLayoutGuide.topAnchor, constant: -16),
        // cardView.heightAnchor.constraint(equalToConstant: 300),
    ])

```

Finally, specify the card view's content:
```swift

    // 3. Set your card view's content.
    let content = UILabel()
    content.text = "Hello world!"
    content.numberOfLines = 0

    cardView.contentView = content

```

---

The card view comes with smart visual defaults (including a shadow), but you
can also customize them:
```swift

    cardView.backgroundColor = .white

    cardView.cornerRadius = 2

    cardView.layer.shadowOffset = CGSize(width: 0, height: 2)
    cardView.layer.shadowRadius = 2
    cardView.layer.shadowOpacity = 0.4

```

Want it? ScrollingCardView is available on CocoaPods: you can find installation
instructions and the source [on GitHub][GitHub].

Questions? Feature requests? [File an issue][issues] or find us on [#mobile][].

---
<a name="Notes"></a>
## Notes
<sub>
1: This particular design was not actually used because we felt we could
provide a better user experience if we also moved the card itself, which lets
the user fill the screen with the long-form content they were trying to read.
Further discussion in [prox#372][].
</sub>

[Prox]: https://medium.com/firefox-ux/project-prox-4e4c4e155592#.qajntoscw
[prox#372]: https://github.com/mozilla-mobile/prox/issues/372
[GitHub]: https://github.com/mozilla-mobile/ScrollingCardView
[issues]: https://github.com/mozilla-mobile/ScrollingCardView/issues
[#mobile]: https://wiki.mozilla.org/IRC
