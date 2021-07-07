---
layout: article
title:  "[Coding Test]bits/stdc++.h 란?"
excerpt: bits/stdc++.h 헤더파일에 대해서 설명합니다.
categories:
  - Algorithm
tags:
  - Algorithm
  - Coding Test
cover: https://user-images.githubusercontent.com/28651727/124687580-23525080-df10-11eb-987b-9a5d97550718.png
---
<div align="center">
<div class="card">
  <div class="card__image">
    <img class="image" src="https://user-images.githubusercontent.com/28651727/124687580-23525080-df10-11eb-987b-9a5d97550718.png"/>
  </div>
</div>
</div>

## #include\<bits/stdc++.h\>

코딩테스트 사이트를 돌아다니다보면 `bits/stdc++.h`라는 이름의 라이브러리를 자주 접할 수 있습니다. `stdc++.h`는 C++의 표준 라이브러리들을 헤더파일로 필요한 라이브러리들을 한번에 include할 수 있어 편리합니다. 또한 헤더파일들을 기억해 쓰는 시간을 줄여주기 때문에 시간에 민감한 대회에서 유용합니다.

`stdc++.h`의 코드를 보면 stack, queue, map, set, vector등 자주 사용하는 C++ STL을 모두 포함하고 있습니다.

하지만 `bits/stdc++.h`는 GNU C++라이브러리의 표준 헤더파일이 아니기 때문에 GCC로 컴파일을 하려할 경우 에러가 발생합니다. 또한 문제해결을 위해 필요한 라이브러리외에 불필요한 다른 라이브러리들도 함께 포함하게 되어 컴파일 시간을 증가시키게 됩니다.

## Appendix

```cxx
// C++ includes used for precompiling -*- C++ -*-

// Copyright (C) 2003-2013 Free Software Foundation, Inc.
//
// This file is part of the GNU ISO C++ Library.  This library is free
// software; you can redistribute it and/or modify it under the
// terms of the GNU General Public License as published by the
// Free Software Foundation; either version 3, or (at your option)
// any later version.
   
// This library is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.

// Under Section 7 of GPL version 3, you are granted additional
// permissions described in the GCC Runtime Library Exception, version
// 3.1, as published by the Free Software Foundation.

// You should have received a copy of the GNU General Public License and
// a copy of the GCC Runtime Library Exception along with this program;
// see the files COPYING3 and COPYING.RUNTIME respectively.  If not, see
// <http://www.gnu.org/licenses/>.

/** @file stdc++.h
 *  This is an implementation file for a precompiled header.
 */

// 17.4.1.2 Headers

// C
#ifndef _GLIBCXX_NO_ASSERT
#include <cassert>
#endif
#include <cctype>
#include <cerrno>
#include <cfloat>
#include <ciso646>
#include <climits>
#include <clocale>
#include <cmath>
#include <csetjmp>
#include <csignal>
#include <cstdarg>
#include <cstddef>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <ctime>

#if __cplusplus >= 201103L
#include <ccomplex>
#include <cfenv>
#include <cinttypes>
#include <cstdalign>
#include <cstdbool>
#include <cstdint>
#include <ctgmath>
#include <cwchar>
#include <cwctype>
#endif

// C++
#include <algorithm>
#include <bitset>
#include <complex>
#include <deque>
#include <exception>
#include <fstream>
#include <functional>
#include <iomanip>
#include <ios>
#include <iosfwd>
#include <iostream>
#include <istream>
#include <iterator>
#include <limits>
#include <list>
#include <locale>
#include <map>
#include <memory>
#include <new>
#include <numeric>
#include <ostream>
#include <queue>
#include <set>
#include <sstream>
#include <stack>
#include <stdexcept>
#include <streambuf>
#include <string>
#include <typeinfo>
#include <utility>
#include <valarray>
#include <vector>

#if __cplusplus >= 201103L
#include <array>
#include <atomic>
#include <chrono>
#include <condition_variable>
#include <forward_list>
#include <future>
#include <initializer_list>
#include <mutex>
#include <random>
#include <ratio>
#include <regex>
#include <scoped_allocator>
#include <system_error>
#include <thread>
#include <tuple>
#include <typeindex>
#include <type_traits>
#include <unordered_map>
#include <unordered_set>
#endif
```