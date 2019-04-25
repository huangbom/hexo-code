---
title: rxjava
date: 2019-04-25 11:22:55
tags: rxjava
category: rxjava
---


## debounce

```kotlin
RxTextView.afterTextChangeEvents(card_types_list_search)
    .debounce(500, TimeUnit.MILLISECONDS)
    .flatMap { event ->
        sendSearchRequest(event.view().text).toObservable()
    }
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe( 
    		{ it ->setData(it.brands)}, 
    		{ throwable -> Timber.d(throwable) }
    )
```

## rxjava 嵌套请求

**`list<A>` 转换成list`<B>`**

```kotlin
fun getRequests(teamId: TeamId): Observable<RelationshipRequestCellViewModel> = 
        teamMemberService.unregisteredPlayers(teamId)
                .listSwitchMap { teamMember ->
                    personService.getRelationshipRequests(teamMember.person.id)
                            .mapMap { Pair(teamMember.person, it) }
                }
                .map { it.flatten() }
                .mapMap {
                    RelationshipRequestCellViewModel(
                            requester = it.second.person,
                            requesteeName = it.first.fullDisplayName,
                            requesteeId = it.first.id,
                            relationshipType = it.second.type,
                            relationshipContext = RelationshipContext.TEAM_HOME
                    )
                }
```

## [链接请求和更新UI](https://codeday.me/bug/20181230/476118.html)

