Page Table of Contents
- [Introduction](#introduction)
- [The HTTP Package](#the-http-package)

## Introduction

In this chapter I will briefly show you how to communicate with the Web in Flutter [(Flutter Dev Team 2018e)](https://flutter.dev/). I think most large scale application are dependant on the Web in one way or another, so it felt important to cover this topic.

## The HTTP Package

Communicating with the Web is very easy in Flutter. The Dart Team maintains an external package called *http* [(Dart Team 2019c)](https://pub.dev/packages/http) which takes care of most of the work for you. Dart [(Dart Team 2019a)](https://dart.dev/) also offers very good integration of asynchrones programming [(Dart Team 2018)](https://dart.dev/codelabs/async-await), which I covered in the [last chapter](https://github.com/Fasust/flutter-guide/wiki/140-Asynchronous-Flutter). Let’s look at an example, this is a simplified version is Wisgens Api Repository. It can make a request the AdviceSlip API [(Kiss 2019)](https://api.adviceslip.com/) to fetch some new advice texts.

``` dart
import 'package:http/http.dart' as http;

class Api implements Repository<Wisdom> {
  static const _adviceURI = 'https://api.adviceslip.com/advice/search/%20';

  Future<List<Wisdom>> loadData() async {
    http.Response response = await http.get(_adviceURI); //API Request

    AdviceSlips adviceSlips = AdviceSlips.fromJson(json.decode(response.body));

    List<Wisdom> wisdoms = new List();
    adviceSlips.slips.forEach((slip) {
      wisdoms.add(slip.toWisdom());
    });

    return wisdoms;
  }
}
```

*Codesnippt 11: Wisgen API Repository [(Faust 2019)](https://github.com/Fasust/wisgen)*

As you can see, you simply call *get()* on the HTTP module and give it the URL it should request. This is an asynchronous call, so you can use the *await* keyword to wait till the request is complete. Once the request is finished, you can read out headers and the body from the http.Response object.

The AdviceSlips class, is generated with a JSON to Dart converter [(Lecuona 2019)](https://javiercbk.github.io/json_to_dart/). The generated class has a fromJson function that makes it easy to populate it’s data fields with the JSON response. This is the generated class, you don’t need to read it all, I just want to give you an idea of how it looks like:

``` dart
class AdviceSlips {
  String totalResults;
  String query;
  List<Slips> slips;

  AdviceSlips({this.totalResults, this.query, this.slips});

  AdviceSlips.fromJson(Map<String, dynamic> json) {
    totalResults = json['total_results'];
    query = json['query'];
    if (json['slips'] != null) {
      slips = new List<Slips>();
      json['slips'].forEach((v) {
        slips.add(new Slips.fromJson(v));
      });
    }
  }

  Map<String, dynamic> toJson() {
    final Map<String, dynamic> data = new Map<String, dynamic>();
    data['total_results'] = this.totalResults;
    data['query'] = this.query;
    if (this.slips != null) {
      data['slips'] = this.slips.map((v) => v.toJson()).toList();
    }
    return data;
  }
}

class Slips {
  String advice;
  String slipId;

  Slips({this.advice, this.slipId});

  Slips.fromJson(Map<String, dynamic> json) {
    advice = json['advice'];
    slipId = json['slip_id'];
  }

  Map<String, dynamic> toJson() {
    final Map<String, dynamic> data = new Map<String, dynamic>();
    data['advice'] = this.advice;
    data['slip_id'] = this.slipId;
    return data;
  }

  //I wrote this function myself to make it easy to cast 
  //slips into my own Wisdom data structure.
  Wisdom toWisdom() {
    return new Wisdom(
      id: int.parse(slipId),
      text: advice,
      type: "Advice Slip",
    );
  }
}
```

*Codesnippt 12: Wisgen AdviceSlips Class [(Faust 2019)](https://github.com/Fasust/wisgen)*

<p align="right"><a href="https://github.com/Fasust/flutter-guide/wiki/200-Architecting-a-Flutter-App">Next Chapter: Architecting a Flutter App ></a></p>
<p align="center"><a href="#">Back to Top</a></center></p>